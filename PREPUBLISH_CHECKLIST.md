# Pre-publish checklist (run before every publish)

Answer every line. Any "no" means stop and fix, or ask Brian.

1. [ ] Brian has OK'd this bot, and this section if it is new.
2. [ ] File name is a short name + 5-6 random characters. No year, no date, no full topic.
3. [ ] It sits in my own folder `{bot}_{id}/` (the only outside change: a one-line hub link proposal, unless I am the hub keeper). No `index.html` added. No `sitemap.xml`.
4. [ ] The head has all 5 noindex tags and all 3 no-cache tags (command below prints 8).
5. [ ] No analytics, trackers, or outside scripts, fonts, or images. Only scripts: the reload line and the optional inline "Last updated" script (document.lastModified only).
6. [ ] Role plus links only.
7. [ ] No tokens, keys, passwords, or secrets.
8. [ ] No grades, money, account numbers, health or meds (anyone's).
9. [ ] No address, phone number, or email address (personal email above all).
10. [ ] No schedule, exam date, or location that shows where Brian is or when anyone is away. No device list.
11. [ ] No photos with faces, plates, house numbers, or location data.
12. [ ] Nothing about other people's bot setups or sites. No link, name, or copied address from any other family member's sites or repos.
13. [ ] Commit message starts with `[bot:{name}]`; author email is a noreply address.
14. [ ] Going to my `bot-{name}` branch with a pull request, not straight to main.
15. [ ] I would be fine seeing this page on a billboard. (Anyone with the link can read it.)

Quick machine check (first prints 8, second prints nothing):

```
grep -c -E 'name="(robots|googlebot|google-extended|gptbot|CCBot)"|http-equiv="(Cache-Control|Pragma|Expires)"' page.html
grep -n -i -E 'ghp_|github_pat_|password|passwd|token=|api[_-]?key|secret|gtag|googletagmanager|analytics|[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}|[0-9]{3}[-. ][0-9]{3}[-. ][0-9]{4}|\$[0-9]' page.html
```

The machine check helps; it does not replace reading the page.
