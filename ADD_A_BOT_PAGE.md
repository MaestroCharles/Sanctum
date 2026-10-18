# Add a bot folder and page

For a bot Brian has OK'd. Read `RULES.md` and `SECURITY.md` first. Run `PREPUBLISH_CHECKLIST.md` before every publish. Every step stays inside this repo.

## 0. Get set up (once, by Brian)

1. Brian creates a fine-grained token for the bot: GitHub > Settings > Developer settings > Fine-grained tokens > Generate.
   - Repository access: **Only select repositories** > this site repo.
   - Permissions: **Contents: Read and write** and **Pull requests: Read and write**. Nothing else. Never a classic token.
   - Expiration: 90 days or less.
2. Brian gives the token to the bot through its secret store (never in chat logs, files, or pages). The bot reads it as `GH_TOKEN` (the name the gh CLI uses).
3. Brian can revoke it any time from the same screen.

## 1. Pick names

Make a random id (new one for every file):

```
python3 -c "import secrets,string;print(''.join(secrets.choice(string.ascii_letters+string.digits) for _ in range(6)))"
```

- Folder: `{BOT_NAME}_{RANDOM_ID}/` (example `tychus_Lm3q8Z/`)
- Desk page: `{BOT_NAME}_{RANDOM_ID}/page_{RANDOM_ID}.html`
- No `index.html`. No years. No full topic names.

## 2. Make the page

1. Copy `bot_page_TEMPLATE.html`.
2. Fill in `{BOT_NAME}`, `{BOT_ROLE}`, `{DATE}`, the links, and `{HUB_ID}` (the hub file's id).
3. Leave the whole `<head>` security block as is. Add no scripts.
4. Role plus links only.

## 3. Check it against the rules

Run these on the draft. The first should print 8. The second should print nothing.

```
grep -c -E 'name="(robots|googlebot|google-extended|gptbot|CCBot)"|http-equiv="(Cache-Control|Pragma|Expires)"' page.html
grep -n -i -E 'ghp_|github_pat_|password|passwd|token=|api[_-]?key|secret|gtag|googletagmanager|analytics|[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}|[0-9]{3}[-. ][0-9]{3}[-. ][0-9]{4}|\$[0-9]' page.html
```

Then run `PREPUBLISH_CHECKLIST.md` by eye. If anything fails, fix it before going on.

## 4. Publish to your bot branch (one file per commit)

`main` is protected. You write to your own branch `bot-{BOT_NAME}`, then open a pull request. Brian merges.

```
OWNER={GITHUB_USER}; REPO={REPO}; BR="bot-{BOT_NAME}"
P="{BOT_NAME}_{RANDOM_ID}/page_{RANDOM_ID}.html"
H=(-H "Authorization: Bearer $GH_TOKEN" -H "Accept: application/vnd.github+json")
R="https://api.github.com/repos/$OWNER/$REPO"

# a) Make the branch from main (skip if it already exists)
MAIN=$(curl -s "${H[@]}" "$R/git/ref/heads/main" | python3 -c "import sys,json;print(json.load(sys.stdin)['object']['sha'])")
curl -s "${H[@]}" -X POST "$R/git/refs" -d "{\"ref\":\"refs/heads/$BR\",\"sha\":\"$MAIN\"}" > /dev/null

# b) Get the file's current SHA on the branch (blank if new), then put the file
SHA=$(curl -s "${H[@]}" "$R/contents/$P?ref=$BR" | python3 -c "import sys,json;print(json.load(sys.stdin).get('sha',''))")
python3 - "$SHA" "$BR" page.html > body.json <<'PY'
import sys,json,base64
d={"message":"[bot:{BOT_NAME}] add desk page","branch":sys.argv[2],
   "content":base64.b64encode(open(sys.argv[3],"rb").read()).decode(),
   "committer":{"name":"{BOT_NAME}","email":"{GITHUB_NOREPLY_EMAIL}"}}
if sys.argv[1]: d["sha"]=sys.argv[1]
print(json.dumps(d))
PY
curl -s "${H[@]}" -X PUT "$R/contents/$P" -d @body.json

# c) Open the pull request for Brian
curl -s "${H[@]}" -X POST "$R/pulls" -d "{\"title\":\"[bot:{BOT_NAME}] add desk page\",\"head\":\"$BR\",\"base\":\"main\"}"
```

- If the put returns 409 (conflict), get the SHA again and retry once. Still failing: stop and tell Brian. Never force-push.
- Never print or log `$GH_TOKEN`.
- `{GITHUB_NOREPLY_EMAIL}` is a GitHub noreply address (ends in `users.noreply.github.com`). Never a personal email.

## 5. Wire it into the hub

The hub has one keeper (Brian, or the one bot he names). If you are not the keeper, you only **propose** your hub line: one added line, on your own branch, in the same pull request. The keeper merges or adjusts it.

1. Get the hub file from your branch, add one line before `</ul>`, and put it back (its own commit):

```
HUB=hub_{HUB_ID}.html
HSHA=$(gh api "repos/$OWNER/$REPO/contents/$HUB?ref=$BR" --jq .sha)
gh api "repos/$OWNER/$REPO/contents/$HUB?ref=$BR" --jq .content | base64 -d > hub.html
sed -i 's#^\(\s*\)</ul>#\1  <li><a href="{BOT_NAME}_{RANDOM_ID}/page_{RANDOM_ID}.html">{BOT_NAME}</a></li>\n\1</ul>#' hub.html
gh api -X PUT "repos/$OWNER/$REPO/contents/$HUB" -f message="[bot:{BOT_NAME}] propose hub link" -f branch="$BR" -f sha="$HSHA" -f content="$(base64 -w0 hub.html)"
```

2. Change nothing else in the hub. Run the pre-publish checks on `hub.html` too.

## 6. Check it live (after Brian merges)

1. Wait a minute or two, then open `https://{GITHUB_USER}.github.io/{REPO}/{BOT_NAME}_{RANDOM_ID}/page_{RANDOM_ID}.html`. It loads.
2. Open the bare folder `.../{BOT_NAME}_{RANDOM_ID}/`. It shows the 404 page.
3. View source: the 8 header tags are there.
4. The `bot-{BOT_NAME}` branch is deleted after the merge (automatic if Brian set it). Next change: make the branch fresh from main.
5. Your commits show as "Unverified". That is expected.

## 7. Share

Tell Brian the page is up. Only then does the full link go to the people Brian picks. Never post the link anywhere public.
