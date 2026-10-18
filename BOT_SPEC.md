# BOT_SPEC

Authoritative spec for the agent guiding setup; other files explain it for Brian. If this file and another file disagree, this file wins; flag the gap to Brian. Placeholders: {OWNER} (Brian's GitHub user, or his org if org_option is used), {REPO}, {BOT}, {ID}, {HUB_ID}, {PAGE_FILE}, {SHA}. Brian runs every "actor: brian" step himself, signed in as himself. Bots never sign in as Brian and never ask for his password.

```yaml
spec_version: 1
owner: brian            # Brian owns repo, settings, rules. Bots draft and open PRs only.
approvals:
  new_bot: brian        # before a bot gets a token
  new_public_section: brian
  share_link: brian     # after merge and live check, to people Brian picks

repo:
  name_pattern: "short plain word, no real name, no school, no year"
  visibility: public    # required for free Pages; public = every file and all history readable
  dedicated: true       # NEW separate repo only
  never:
    - enable Pages on an existing repo
    - put site files in an existing repo
    - add a GitHub remote to a private local-only repo
  private_source_material: "separate private repo, or nowhere"
  pages:
    source: deploy_from_branch
    branch: main
    path: "/"           # "/docs" allowed; then all site files live under /docs
    custom_domain: none
    https_enforced: true   # already on for github.io; confirm
  features_off: [wiki, issues, discussions, projects]   # unless Brian uses them

account:
  two_factor: required
  two_factor_type: passkey_or_security_key   # authenticator app acceptable; no SMS
  recovery_codes: "download; store offline (printed or password manager); never in a repo or email"
  email_private: true                        # Settings > Emails: keep private + block pushes that expose email
  commit_email: "{id}+{username}@users.noreply.github.com"   # the committing account; under org_option each bot uses its own

org_option:              # stronger: real 1-review lock
  why: "fine-grained tokens act as Brian, so a bot could open AND merge its own PR; Brian cannot approve a PR shown as his own"
  note: "fine-grained tokens do not work for collaborators on a personal repo, so this needs an org"
  steps:
    - Brian creates a free GitHub organization he owns
    - "org settings: require two-factor authentication; base (default) member permission = No permission"
    - Brian transfers or creates the site repo in that org
    - "each bot gets its own GitHub account with 2FA and joins as an org MEMBER, not an outside collaborator (outside collaborators on org repos can use only classic tokens)"
    - Brian gives each bot member Write on the site repo only
    - each bot account creates its own fine-grained token, resource owner = the org, site repo only
    - each bot commits with its own noreply address
    - ruleset pull_request.required_approving_review_count = 1
  site_address_changes_to: "{org}.github.io/{REPO}"   # update hub bookmark and shared links
  limit: "GitHub allows one free machine account per person, so per-bot accounts may not be possible; likely later path is a GitHub App per bot, installed on the site repo only"
  default: off            # without it, "Brian merges" is an honor rule; bots never merge

repo_settings:
  secret_scanning: enabled
  secret_scanning_push_protection: enabled
  dependabot_alerts: enabled
  delete_branch_on_merge: true
  ruleset_main:
    name: protect-main
    target: branch
    include: ["~DEFAULT_BRANCH"]
    enforcement: active
    bypass_actors: []     # nobody, not even Brian (bot tokens act as Brian)
    rules:
      - deletion
      - non_fast_forward  # blocks force-push
      - pull_request      # required_approving_review_count: 0, or 1 with org_option

bots:
  branch_pattern: "bot-{BOT}"
  writes_to_main: never
  own_folder_only: true
  hub_keeper: "brian, or one bot brian names"   # only the keeper edits the hub directly
  hub_link_by_other_bots: "propose one added <li> line in the hub, on the bot branch, in the same PR; keeper merges or adjusts"
  merges_own_pr: never
  one_file_per_commit: true
  commit_message_prefix: "[bot:{BOT}] "
  on_conflict_409: "re-read sha, retry once, then stop and tell Brian"
  force_push: never
  after_merge: "branch deleted (auto); next change starts a fresh branch from main"
  commits_show_unverified: expected
  scheduled_publishing: none   # default; Brian can change

tokens:
  type: fine_grained          # never classic, never all-repositories
  per_bot: true
  reuse: never                # never reuse or widen a token made for another repo
  repository_access: only_select_repositories
  repositories: ["{REPO}"]
  permissions:
    contents: write           # includes read
    pull_requests: write
    metadata: read            # automatic
    everything_else: none
  expiry_days: 90
  rotation: "new token before expiry; hand to bot via its secret store; delete old token"
  revoke: "Settings > Developer settings > Fine-grained tokens > Delete; immediate"
  storage: "bot secret store / env var GH_TOKEN only (one name everywhere); never in pages, files, commits, chats, issues, logs"
  create_url: "https://github.com/settings/personal-access-tokens/new?name={BOT}-site&expires_in=90&contents=write&pull_requests=write"

actions:
  secrets: none               # unless truly needed
  never_echo_secrets: true
  workflows: none             # default; Pages deploys from branch

commit_author:
  email: noreply_only         # users.noreply.github.com; never Brian's login email or any personal email

layout:
  paths:
    - "/hub_{HUB_ID}.html"           # the one map; Brian bookmarks it
    - "/{BOT}_{ID}/page_{ID}.html"   # one folder per bot
    - "/game_{ID}/"                  # later: web game, same rules
    - "/robots.txt"
    - "/404.html"
  never: [index.html, sitemap.xml, "folder listings", "dates or years in names"]
  id_format: "[A-Za-z0-9]"
  id_length: 6                     # 5-6 allowed; new id per file
  id_command: "python3 -c \"import secrets,string;print(''.join(secrets.choice(string.ascii_letters+string.digits) for _ in range(6)))\""

head_tags_required: |
  <meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex">
  <meta name="googlebot" content="noindex, nofollow">
  <meta name="google-extended" content="noindex, nofollow">
  <meta name="gptbot" content="noindex, nofollow">
  <meta name="CCBot" content="noindex, nofollow">
  <meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate">
  <meta http-equiv="Pragma" content="no-cache">
  <meta http-equiv="Expires" content="0">
head_tags_also: |
  <meta name="referrer" content="no-referrer">
head_tag_caveats:
  - "browsers mostly ignore no-cache metas; Pages caches ~10 min; a removed page can linger a few minutes"
  - "disallow-all robots.txt means crawlers never see noindex; a linked URL can still be listed with no content"
  - "gptbot, CCBot, google-extended are honored as robots.txt user agents, not as meta tags"

robots_txt: |
  User-agent: *
  Disallow: /

  User-agent: GPTBot
  Disallow: /

  User-agent: ClaudeBot
  Disallow: /

  User-agent: CCBot
  Disallow: /

  User-agent: Google-Extended
  Disallow: /

no_tracking: [analytics, trackers, pixels, ads, chat widgets, share buttons, outside scripts, outside fonts, outside images]
allowed_scripts:               # the only scripts allowed; inline only
  reload: "window.onpageshow = function (e) { if (e.persisted) { window.location.reload(); } };"
  last_updated:                 # optional; document.lastModified only; no network calls, no outside loads, no storage
    html: '<span id="lastUpdated"></span>'
    js: 'var d = new Date(document.lastModified); document.getElementById("lastUpdated").textContent = "Last updated: " + d.toLocaleDateString() + " " + d.toLocaleTimeString([], {hour: "2-digit", minute: "2-digit"});'
  never: [fetch, XMLHttpRequest, localStorage, sessionStorage, cookies, "script src", eval]

bot_page_content: role_plus_links_only
max_briefs_per_page: 6          # default; Brian can change

forbidden_content:
  - tokens, API keys, passwords, recovery codes, security answers, any secret
  - grades, transcripts, school records
  - money, bills, balances, account numbers, card numbers
  - health or meds, about anyone, ever
  - accounts, usernames, logins
  - home or dorm address, phone numbers
  - any email address, personal email above all
  - schedules, exam dates, travel dates, or locations showing where Brian is or when anyone is away
  - device lists (computers, phones, gear)
  - photos with faces, plates, house numbers, or location data
  - links to private documents or drives
  - other people's private info
  - anything about other people's bot setups or sites (paths, bot names, repo names, links)
  - links, names, or copied addresses from any other family member's sites or repos (in the repo, pages, or commits); model the look, not the links

obscurity_is_not_access_control: "slugs only cut drive-by discovery; anyone with the link can read a page; the repo file list shows every path"

prepublish_checks:              # run on each page HTML file F (hub and bot pages); any failure = stop. 404.html: all except opaque_name. Not for .md or robots.txt
  - name: eight_head_tags
    cmd: "test $(grep -c -E 'name=\"(robots|googlebot|google-extended|gptbot|CCBot)\"|http-equiv=\"(Cache-Control|Pragma|Expires)\"' \"$F\") -eq 8"
  - name: no_secrets_or_pii
    cmd: "! grep -n -i -E 'ghp_|github_pat_|password|passwd|token=|api[_-]?key|secret|gtag|googletagmanager|analytics|[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}|[0-9]{3}[-. ][0-9]{3}[-. ][0-9]{4}|\\$[0-9]' \"$F\""
  - name: no_outside_scripts
    cmd: "! grep -n -i -E '<script[^>]+src=|<link[^>]+href=\"https?:|<img[^>]+src=\"https?:|<iframe' \"$F\""
  - name: opaque_name
    cmd: "echo \"$F\" | grep -q -E '_[A-Za-z0-9]{5,6}\\.html$' && ! echo \"$F\" | grep -q -E '(19|20)[0-9]{2}'"
  - name: no_index_or_sitemap
    cmd: "! git ls-files | grep -q -E '(^|/)(index\\.html|sitemap\\.xml)$'"
  - name: ascii_only
    cmd: "! LC_ALL=C grep -q -P '[^\\x00-\\x7F]' \"$F\""
  - name: human_read
    cmd: "manual: read page against forbidden_content; role plus links only; billboard test"

incident_steps:
  - "1 rotate: Brian revokes the token now; change any other leaked key at its source; new token only if still needed"
  - "2 stop exposure: make repo private or unpublish Pages (gh api -X DELETE repos/{OWNER}/{REPO}/pages)"
  - "3 check commits: git log --all -p | grep -n -i -E '<leaked string>' ; deleted text stays in history"
  - "4 rewrite history if a secret or personal data got in (Brian only, with a history-rewrite tool); ask GitHub Support to clear cached views if needed"
  - "5 tell Brian and his dad what leaked, when, and what was done"
  - "6 add a dated rule change to RULES.md if needed"

setup_steps:                    # in order; brian steps use Brian's own gh login
  - {n: 1,  actor: brian, do: "turn on 2FA (passkey or security key); store recovery codes offline", cmd: "web: Settings > Password and authentication"}
  - {n: 2,  actor: brian, do: "keep email private; block pushes that expose email", cmd: "web: Settings > Emails"}
  - {n: 3,  actor: brian, do: "sign in gh CLI as himself", cmd: "gh auth login"}
  - {n: 4,  actor: brian, do: "create new public repo, wiki and issues off", cmd: "gh repo create {REPO} --public --disable-wiki --disable-issues"}
  - {n: 5,  actor: brian, do: "projects and discussions off; auto-delete head branches on", cmd: "gh api -X PATCH repos/{OWNER}/{REPO} -F has_projects=false -F has_discussions=false -F delete_branch_on_merge=true"}
  - {n: 6,  actor: brian, do: "secret scanning + push protection on", cmd: "echo '{\"security_and_analysis\":{\"secret_scanning\":{\"status\":\"enabled\"},\"secret_scanning_push_protection\":{\"status\":\"enabled\"}}}' | gh api -X PATCH repos/{OWNER}/{REPO} --input -"}
  - {n: 7,  actor: bot,   do: "draft first files for Brian to add: robots.txt, 404.html, hub_{HUB_ID}.html, RULES.md, SECURITY.md, PREPUBLISH_CHECKLIST.md, INCIDENT.md, ADD_A_BOT_PAGE.md, BOT_SPEC.md", cmd: "none (draft only; run prepublish_checks only on page HTML files: hub_{HUB_ID}.html and every {BOT}_{ID}/page_{ID}.html; 404.html is exempt from opaque_name; never run them on .md files or robots.txt)"}
  - {n: 8,  actor: brian, do: "commit first files to main before the ruleset exists", cmd: "git clone https://github.com/{OWNER}/{REPO} && cd {REPO} && git add . && git commit -m 'site skeleton' && git push"}
  - {n: 9,  actor: brian, do: "check history before Pages goes live", cmd: "git log --all -p | grep -n -i -E 'ghp_|github_pat_|password|secret|@gmail|@outlook'"}
  - {n: 10, actor: brian, do: "ruleset on main, bypass nobody", cmd: "echo '{\"name\":\"protect-main\",\"target\":\"branch\",\"enforcement\":\"active\",\"bypass_actors\":[],\"conditions\":{\"ref_name\":{\"include\":[\"~DEFAULT_BRANCH\"],\"exclude\":[]}},\"rules\":[{\"type\":\"deletion\"},{\"type\":\"non_fast_forward\"},{\"type\":\"pull_request\",\"parameters\":{\"required_approving_review_count\":0,\"dismiss_stale_reviews_on_push\":false,\"require_code_owner_review\":false,\"require_last_push_approval\":false,\"required_review_thread_resolution\":false}}]}' | gh api -X POST repos/{OWNER}/{REPO}/rulesets --input -"}
  - {n: 11, actor: brian, do: "turn on Pages from main /", cmd: "gh api -X POST repos/{OWNER}/{REPO}/pages -f 'source[branch]=main' -f 'source[path]=/'"}
  - {n: 12, actor: brian, do: "confirm HTTPS enforced", cmd: "gh api -X PUT repos/{OWNER}/{REPO}/pages -F https_enforced=true"}
  - {n: 13, actor: brian, do: "verify settings", cmd: "gh api repos/{OWNER}/{REPO} --jq '{visibility,has_wiki,has_issues,delete_branch_on_merge,security_and_analysis}' && gh api repos/{OWNER}/{REPO}/rulesets"}
  - {n: 14, actor: brian, do: "OK a bot; create its token (pick Only select repositories > {REPO}); give it via the bot's secret store", cmd: "web: open tokens.create_url"}
  - {n: 15, actor: bot,   do: "make bot branch from main", cmd: "SHA=$(gh api repos/{OWNER}/{REPO}/git/ref/heads/main --jq .object.sha) && gh api -X POST repos/{OWNER}/{REPO}/git/refs -f ref=refs/heads/bot-{BOT} -f sha=$SHA"}
  - {n: 16, actor: bot,   do: "run prepublish_checks, then put page (one file per commit; add -f sha={SHA} when updating)", cmd: "gh api -X PUT repos/{OWNER}/{REPO}/contents/{BOT}_{ID}/page_{ID}.html -f message='[bot:{BOT}] add page' -f branch=bot-{BOT} -f content=\"$(base64 -w0 {PAGE_FILE})\""}
  - n: 17
    actor: bot
    do: "propose hub link: one added line before </ul> on the bot branch, separate commit, current sha (keeper edits the hub directly instead)"
    cmd: |
      HUB=hub_{HUB_ID}.html
      HSHA=$(gh api "repos/{OWNER}/{REPO}/contents/$HUB?ref=bot-{BOT}" --jq .sha)
      gh api "repos/{OWNER}/{REPO}/contents/$HUB?ref=bot-{BOT}" --jq .content | base64 -d > hub.html
      sed -i 's#^\(\s*\)</ul>#\1  <li><a href="{BOT}_{ID}/page_{ID}.html">{BOT}</a></li>\n\1</ul>#' hub.html
      gh api -X PUT "repos/{OWNER}/{REPO}/contents/$HUB" -f message="[bot:{BOT}] propose hub link" -f branch=bot-{BOT} -f sha="$HSHA" -f content="$(base64 -w0 hub.html)"
  - {n: 18, actor: bot,   do: "open PR for Brian; never merge it", cmd: "gh api -X POST repos/{OWNER}/{REPO}/pulls -f title='[bot:{BOT}] add page' -f head=bot-{BOT} -f base=main"}
  - {n: 19, actor: brian, do: "review and merge PR", cmd: "gh pr merge <n> --repo {OWNER}/{REPO} --squash"}
  - {n: 20, actor: bot,   do: "check live: page 200, bare folder 404, 8 tags in source", cmd: "curl -s -o /dev/null -w '%{http_code}\\n' https://{OWNER}.github.io/{REPO}/{BOT}_{ID}/page_{ID}.html ; curl -s -o /dev/null -w '%{http_code}\\n' https://{OWNER}.github.io/{REPO}/{BOT}_{ID}/"}
  - {n: 21, actor: brian, do: "share full link only with people he picks", cmd: "none"}
  - {n: 22, actor: brian, do: "every 90 days: rotate each bot token; remove a bot = delete its token + its hub line via PR", cmd: "web: Settings > Developer settings > Fine-grained tokens"}

out_of_scope: ["hosting outside GitHub Pages (for example Netlify) - later option", "any custom domain"]
```
