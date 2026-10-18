# Security - every measure, in plain English

This site is Brian's. It is built so bots can put up useful pages without exposing Brian. Each measure below says what it is and why.

## The two facts that drive everything

1. **A GitHub Pages site from a public repo is fully public, history included.** Anyone who finds the repo can read every file and every old version of it.
2. **Obscurity is not access control.** Random names only cut down drive-by discovery (search engines, guessing). Anyone with the link can read the page, and the repo's file list on github.com shows every page path. So truly private things never go in this repo. Their source goes in a separate private repo, or stays out of GitHub entirely.

## Obscurity (keeps pages out of search and hard to stumble on)

- **Random slugs.** Every file is a short name plus 5-6 random letters/digits (`page_q7Rk2P.html`). No years, dates, or full topic names.
- **No index pages in bot folders.** A bare folder address shows the 404 page, not a list.
- **No sitemap** and no "all pages" list. The hub is the only map, and it has a random name too.
- **robots.txt disallows everything**, and names the AI crawlers GPTBot, ClaudeBot, CCBot and Google-Extended.
- **Five noindex tags on every page:** `robots` (noindex, nofollow, noarchive, nosnippet, noimageindex), plus `googlebot`, `google-extended`, `gptbot`, `CCBot` (each noindex, nofollow).
- **Three no-cache tags on every page:** `Cache-Control` (no-cache, no-store, must-revalidate), `Pragma` (no-cache), `Expires` (0). They help a little, but browsers mostly ignore them and GitHub Pages caches for about 10 minutes. **A removed page can linger a few minutes.**
- Caveat: disallow-all means crawlers never fetch a page, so they never see its noindex; a linked URL can still show up in search as a bare address with no content. And `gptbot`, `CCBot` and `google-extended` are robots.txt user-agent names, not meta tags crawlers honor; the robots.txt lines are what count for them.
- **No-referrer tag** so the page's address is not passed to sites it links to.
- **Custom 404.html** with the same header, so missing pages say nothing.

## No tracking

- No analytics, trackers, pixels, ads, chat widgets, or share buttons.
- No scripts, fonts, or images loaded from other sites. Allowed scripts: a one-line back-button reload, and an optional inline "Last updated" script that only reads `document.lastModified` and writes it into the page (inline, no network calls, nothing loaded from other sites, no storage).

## No secrets, no personal data

Never on a page, in a file, or in a commit:

- Tokens, keys, passwords, recovery codes, security answers.
- Grades, school records, money, bills, account or card numbers.
- **Health or meds, about anyone, ever.**
- Home or dorm address, phone numbers, **any email address** (personal email above all).
- **Schedules or locations** that show where Brian is or when anyone is away, including exam dates.
- Device lists (computers, phones, gear).
- Photos with faces, plates, house numbers, or location data.
- Anything about other people's bot setups or sites (their paths, bot names, repo names, links).
- Never link to, name, or copy addresses from any other family member's sites or repos into this repo, its pages, or its commits. Model the look, not the links.

Bot pages are **role plus links only.**

## The account

- **2FA on Brian's GitHub account**, ideally a passkey or security key. Recovery codes stored offline.
- **Private email setting on**, so commits show a `users.noreply.github.com` address, never Brian's login email.

## Least privilege

- **One new fine-grained token per bot**, scoped to **this site repo only** (never reused from another repo), with Contents and Pull requests read/write and nothing else.
- **90-day expiry.** Rotate before it runs out. Brian can revoke any token any time.
- **Never a classic token or an all-repositories token.**
- Tokens live only in each bot's secret store. Never in pages, files, commits, chats, or issues.
- **No GitHub Actions secrets** unless truly needed; never echo a secret in a workflow log.
- Bots never sign in as Brian and never ask for his password.

## Guard rails on the repo

- **Secret scanning and push protection on.** GitHub blocks pushes with known token shapes.
- **Branch protection on main.** Bots work on their own `bot-{name}` branch and open a pull request. Brian merges. Force pushes and deletion of main are blocked. Bypass list: nobody.
- **Honor rule, not a lock:** every bot token acts as Brian, so a bot could open and merge its own pull request through the API, and Brian cannot approve a pull request that shows as his own. Bots never merge. Stronger option: each bot gets its own GitHub account as a member of an org Brian owns, with Write on the site repo only and 1 approving review required (`NEW_REPO_SETUP.md` step 8b).
- Bot branches are deleted after each merge. Bot commits show as "Unverified"; that is expected.
- Unused features (wiki, issues, discussions, projects) off.
- **A dedicated repo.** Pages is never turned on for an existing repo, site files never go in an existing repo, and no remote is added to a private local-only repo.
- **Every bot commit starts with `[bot:{name}]`**, so Brian can see who changed what.
- **Check git history before going public.** Deleted text stays in old commits.

## People in the loop

- **Brian OKs each new bot** before it gets a token, and **any new public section** before it goes up.
- A bot runs `PREPUBLISH_CHECKLIST.md` before every publish.
- Brian's merge is the review. The link is shared only after that, only with people Brian picks.

## If something leaks

Follow `INCIDENT.md`. In short: revoke and rotate the token, take the page down (or make the repo private), check the commits, rewrite history if a secret got in, and tell Brian and his dad. Editing the file alone is not enough.
