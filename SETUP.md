# Deploying adherence.ae on GitHub Pages

## 1. Create the GitHub repo

Pick an account/org to host under (e.g. an `adherence-ae` org). The repo must be named exactly:

```
<account>.github.io
```

For example: `adherence-ae/adherence-ae.github.io`. Make it **public** (Pages on private repos requires a paid plan).

## 2. Push the files

From the folder containing `index.html`, `CNAME`, `favicon.svg`, and this file:

```sh
git init
git add .
git commit -m "initial site"
git branch -M main
git remote add origin git@github.com:<account>/<account>.github.io.git
git push -u origin main
```

## 3. Enable GitHub Pages

In the repo: **Settings → Pages**.
- **Source**: Deploy from a branch
- **Branch**: `main` / `/ (root)`
- Save.

Within ~30s the site will be live at `https://<account>.github.io`. The `CNAME` file already in the repo will populate the "Custom domain" field as `adherence.ae`.

## 4. Configure DNS on tasjeel.ae

Log in to tasjeel.ae, open the DNS / Zone manager for `adherence.ae`, and add:

**Apex (adherence.ae) — A records to GitHub Pages:**

```
@   A   185.199.108.153
@   A   185.199.109.153
@   A   185.199.110.153
@   A   185.199.111.153
```

Optionally also AAAA records for IPv6:

```
@   AAAA   2606:50c0:8000::153
@   AAAA   2606:50c0:8001::153
@   AAAA   2606:50c0:8002::153
@   AAAA   2606:50c0:8003::153
```

**www subdomain — CNAME to the GitHub Pages host:**

```
www   CNAME   <account>.github.io.
```

(Trailing dot if tasjeel's UI accepts FQDN; otherwise just `<account>.github.io`.)

Remove any conflicting records (existing A, AAAA, or CNAME on `@` or `www`) before saving.

## 5. Wait, then enforce HTTPS

DNS propagation in the .ae TLD is usually quick (minutes) but can take up to a few hours. Once `https://adherence.ae` loads the site, go back to **Settings → Pages** and tick **Enforce HTTPS**. GitHub provisions the Let's Encrypt certificate automatically; this option is only available after DNS resolves correctly.

## 6. Sanity checks

```sh
dig adherence.ae +short             # should return the four GitHub IPs
dig www.adherence.ae +short         # should return the .github.io CNAME chain
curl -I https://adherence.ae        # should return 200 with x-github-request-id
```

## Updating the site later

Edit `index.html`, commit, push. Pages redeploys in under a minute.

---

**Reference:** GitHub Pages custom domain docs at `https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site`. The IPs above are GitHub's current published Pages apex addresses — if these stop working, check that page for updates.
