# Site rules

This site belongs to Brian. See also `SECURITY.md` (every measure, explained), `PREPUBLISH_CHECKLIST.md`, and `INCIDENT.md`. These are the rules for every page and every bot that writes here. Bots draft and publish only what Brian allows. Brian can change any rule marked **[default]**. Rules marked **[keep]** protect his privacy and security; change them only on purpose.

## 1. The big idea

- This repo is public (free GitHub Pages needs that). Anyone who finds the repo can read every file in it, including old commits. Random names keep pages out of search, but they do not lock anything.
- So: **if it would hurt to see it on a billboard, it never goes in this repo.** Not on a page, not in a commit, not in a draft. Personal-data pages stay out of this repo entirely. If a page needs personal source material, that material lives in a separate private repo, or nowhere. **[keep]**
- Obscurity only cuts down drive-by discovery. Anyone with the link can read the page. **[keep]**
- The repo's file list on github.com shows every page path. Random names hide nothing from someone browsing the repo. **[keep]**

## 2. Who owns what

- Brian owns the repo, the settings, and every rule here. **[keep]**
- Brian OKs each new bot before it gets write access, and any new public section before it goes up. **[keep]**
- Brian reviews a page before its link is shared with anyone. **[keep]**
- Before a bot's first page is shared, one more person Brian picks (for example his dad) takes a look. **[default]**
- Each bot writes only in its own folder. It may read other folders. **[default]**
- **The hub has one keeper:** Brian, or one bot Brian names (for example his chief bot). Only the keeper edits the hub directly. Any other bot proposes its hub link as a one-line change in its own pull request; the keeper merges or adjusts it. That one line is the only change a bot may propose outside its own folder. **[default]**

## 3. Names and paths

- Every file name is a short name plus 5-6 random letters/digits, like `page_q7Rk2P.html` or `notes_X4k9Pm.html`. **[keep]**
- No years, dates, or full topic names in file or folder names. Readable words are fine in the link text on the page. **[keep]**
- One folder per bot: `{bot}_{random id}/`, for example `tychus_Lm3q8Z/`. **[default]**
- Later projects (for example a web game) get their own opaque folder, `game_{random id}/`, under the same rules. **[default]**
- **No `index.html` in any bot folder,** so the bare folder address shows a 404. **[keep]**
- Layout: `/hub_{id}.html`, `/{bot}_{id}/page_{id}.html`, `/robots.txt`, `/404.html`. **[default]**
- The hub (the map page) is also opaque: `hub_{random id}.html`. Brian bookmarks it. No `index.html` at the root either. **[default]**
- No sitemap (`sitemap.xml`), no sitemap plugin, no folder listings, no "all pages" page other than the hub. **[keep]**

## 4. The security header (every page)

Every HTML page carries these in its `<head>`, exactly. No exceptions. **[keep]**

Five robots tags:

```html
<meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex">
<meta name="googlebot" content="noindex, nofollow">
<meta name="google-extended" content="noindex, nofollow">
<meta name="gptbot" content="noindex, nofollow">
<meta name="CCBot" content="noindex, nofollow">
```

Three no-cache tags:

```html
<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate">
<meta http-equiv="Pragma" content="no-cache">
<meta http-equiv="Expires" content="0">
```

Plus (recommended): `<meta name="referrer" content="no-referrer">` so a page's address is not passed to sites it links to, and the small reload script in the templates so the back button never shows a stale copy. **[default]**

## 5. robots.txt

- `robots.txt` at the site root says Disallow all, and names GPTBot, ClaudeBot, CCBot and Google-Extended. **[keep]**
- Note: crawlers only read `robots.txt` at the very top of the web address. If this site is a project site (`{user}.github.io/{repo}/`), put a copy in a `{user}.github.io` repo too. The page tags still keep search engines from indexing pages, but AI crawlers go by `robots.txt` only.

## 6. No tracking

- No analytics, trackers, pixels, ad code, chat widgets, or "like" buttons. **[keep]**
- No scripts from other sites. Allowed scripts: the tiny reload line in the templates, and an optional inline "Last updated" script that only reads `document.lastModified` and writes it into the page (inline, no network calls, nothing loaded from other sites, no storage). The exact code is in `BOT_SPEC.md` (`allowed_scripts`). Nothing else. **[keep]**
- No embedded fonts, images or videos loaded from other sites. Put images in the repo, and only if they show nothing private. **[default]**

## 7. What never goes on a page or in the repo

**[keep]** for all of these:

- Grades, transcripts, or school records.
- Money: dollar amounts, bills, balances, account numbers, card numbers.
- Health or meds, for anyone, ever.
- Accounts, usernames, logins, passwords, security questions.
- Tokens, API keys, secrets of any kind (also not in commit history; see section 9).
- Home or dorm address, phone numbers, and any email address (personal email above all).
- Schedules or plans that show where Brian is or when anyone is away (travel dates, exam dates, "home empty this week", class locations by time).
- Device lists (what computers, phones, or gear Brian has).
- Photos with faces, license plates, house numbers, or location data.
- Links to private documents or drives.
- Anything about other people's bot setups or sites: their paths, bot names, repo names, or links.
- Never link to, name, or copy addresses from any other family member's sites or repos into this repo, its pages, or its commits. Model the look, not the links.
- Other people's private info.

## 8. What a bot page holds

- **Role plus links.** Who the bot is, what it helps with in one or two lines, and a few safe links. **[keep]**
- At most six short briefs (panels) per page; then start a new opaque page. **[default]**
- Public, well-known links only (school home page, a public calendar page, a tool's docs). **[default]**
- No pages that are just a dump of notes or logs. **[default]**

## 9. Tokens and access

- Brian's GitHub account has 2FA on, ideally a passkey or security key, with recovery codes stored offline. **[keep]**
- Each bot gets its **own, new** fine-grained GitHub token (never one reused from another repo), scoped to **this one repo only**, with only `Contents: Read and write` and `Pull requests: Read and write` (plus the automatic `Metadata: Read`). Never a classic token. Never an all-repositories token. **[keep]**
- Tokens expire in 90 days or less. Brian can revoke any token at any time in GitHub Settings > Developer settings > Fine-grained tokens. **[keep]**
- A token lives in the bot's own secret store or environment. It is never pasted into a page, a file, a commit, a chat log, or an issue. **[keep]**
- No GitHub Actions secrets unless truly needed. If one is used, never print it in a workflow log. **[keep]**
- If a token is ever exposed, follow `INCIDENT.md`. Deleting the file is not enough; old commits keep it. **[keep]**
- Secret scanning and push protection stay on. **[keep]**
- Bots never sign in as Brian and never ask for his password. **[keep]**

## 10. How bots commit

- `main` is protected. Bots never push to `main`. Each bot works on its own branch, `bot-{name}`, and opens a pull request. Brian merges it. That merge is his review. **[keep]**
- Plainly: "Brian merges" is an **honor rule, not a lock**. Bot tokens act as Brian, so a bot could open and merge its own pull request through the API, and Brian cannot approve a pull request that shows as his own. Bots never merge. For a real lock, use the stronger option in `NEW_REPO_SETUP.md` step 8b (a GitHub account per bot, 1 approving review required). **[default]**
- The bot branch is deleted after each merge (or rebuilt fresh from main before the next change). **[default]**
- Bot commits made through the API will show as "Unverified". That is expected; the `[bot:{name}]` prefix says who made them.
- Every commit message starts with `[bot:{name}]`, for example `[bot:tychus] add desk page`. **[keep]**
- Commits use a GitHub noreply address as the author email, never Brian's login email or any personal email. **[keep]**
- Get the current version first. One file per commit. **[default]**
- If the push is rejected (someone else changed it), re-pull and retry once. Never force-push. **[keep]**
- No scheduled publishing jobs. Publish when Brian or his chief bot asks. **[default]**

## 11. Before anything is published

The bot checks the page against this file:

1. File name is opaque (name + random id, no year).
2. All 5 robots tags and 3 no-cache tags are in the head.
3. No analytics, no outside scripts.
4. Nothing from section 7.
5. Role plus links only.
6. Brian has OK'd the bot and the section.

The full list is `PREPUBLISH_CHECKLIST.md`. If any answer is no, it does not publish. Brian merges the pull request (his review) and checks the live page before its link is shared, and the link goes only to people Brian picks.

## 12. Changing these rules

Brian changes this file. Bots may suggest changes; they do not make them. When a **[default]** changes, note the date and the new rule here.
