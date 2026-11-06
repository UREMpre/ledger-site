# ledger.615.media

These files are the universal-link side of Plaid OAuth for the Money Laundering app (bundle
`media.615.ledger`, team `Y8TQ98F5N3`): `.well-known/apple-app-site-association` tells iOS the
`https://ledger.615.media/plaid/oauth*` links belong to the app; `plaid/oauth.html` (served at `/plaid/oauth`) is the page a
bank's OAuth flow lands on if the app isn't installed. Needed only for OAuth banks (Amex, Regions, ...).

**Hosting:** GitHub Pages from the public repo `UREMpre/ledger-site` (its own copy of this folder plus a
`CNAME` file). DNS: `ledger CNAME urempre.github.io` at GoDaddy (615.media's DNS; the `*` wildcard A record
is overridden by the explicit CNAME). Cloud Run was tried first and dropped: austyn@615.media can deploy
there but cannot make a service public or map a domain.

**To change these files:** edit them here, then copy them into the `ledger-site` checkout and push
(pull first: GitHub commits `Delete CNAME` / `Create CNAME` there whenever the custom domain is touched):
`cp -R web/. ~/Downloads/ledger-site/ && (cd ~/Downloads/ledger-site && git pull --ff-only && git add -A && git commit -m "..." && git push)`.

**HTTPS:** live since 2026-11-06 (Let's Encrypt via GitHub Pages, `https_enforced` on, HTTP 301s to HTTPS).
Issuance took ~3 h after the domain was added, and the Pages API/Settings page kept saying "certificate request
process will begin shortly" for an hour after the cert was already being served, so check the real thing:
`openssl s_client -servername ledger.615.media -connect ledger.615.media:443 </dev/null | openssl x509 -noout -subject -dates`.
Removing and re-adding the domain neither helped nor hurt.

**Checks:** `curl -sI https://ledger.615.media/.well-known/apple-app-site-association` must be 200 with no
`Location` header (GitHub serves it as `application/octet-stream`, which Apple accepts);
`curl -s https://app-site-association.cdn-apple.com/a/v1/ledger.615.media` shows what Apple's CDN has — it is
404 until a device with the app's associated-domains entitlement first pulls it, then refreshes within hours.

**Plaid side (manual):** add `https://ledger.615.media/plaid/oauth` under Allowed redirect URIs in the Plaid
Dashboard (Team Settings → API). Without it `link/token/create` fails even in Sandbox.
