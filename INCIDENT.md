# Incident card (something leaked)

Do these in order. Fast beats perfect.

1. **Rotate the token.** Revoke it now (Settings > Developer settings > Fine-grained tokens > Delete). If it was a different key or password, change it at its source. Then make a new token only if still needed.
2. **Stop the exposure.** Make the repo private, or take Pages down (Settings > Pages > unpublish), until it is fixed.
3. **Check the commits.** Find every commit that holds the leaked item. Remember: deleting the file does not remove it from history.
4. **Rewrite history if a secret or personal data got in** (Brian does this, not a bot): remove it from all commits with a history-rewrite tool, push the cleaned history, and ask GitHub Support to clear cached views if needed. Already-revoked tokens are dead either way.
5. **Tell Brian and his dad** what leaked, when, and what was done.
6. Write one line in `RULES.md` under "Changing these rules" if a rule needs to change so it does not happen again.
