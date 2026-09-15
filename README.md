# Reyane Antoine — portfolio site

Everything needed to host the portfolio at a custom domain, free, on GitHub Pages.

| File | What it is |
|---|---|
| `index.html` | The whole site. Fonts come from Google; every image is embedded, so there are no other assets to upload. |
| `Reyane-Antoine-Portfolio.pdf` | The attachable version. The "Download portfolio (PDF)" buttons on the page point at this file, so it has to sit next to `index.html`. |
| `.nojekyll` | Tells GitHub to serve the files as-is instead of running them through Jekyll. |

There is deliberately no `CNAME` file. Setting the custom domain in the Pages settings (step 3) creates one for you with the right contents, which avoids a typo breaking the site.

---

## 1. Create the repo and push

From inside this folder (`~/repos/reyane-portfolio`).

```bash
git init -b main
git add .
git commit -m "Portfolio site"
git remote add origin https://github.com/agolden5/reyane-portfolio.git
git push -u origin main
```

Create the repo first at https://github.com/new — name it `reyane-portfolio`, **Public** (Pages needs public on a free account), and do **not** tick "Add a README", .gitignore, or license, or the push above gets rejected.

With the `gh` CLI installed and signed in, this does all of it in one line, repo creation included:

```bash
gh repo create reyane-portfolio --public --source=. --remote=origin --push
```

---

## 2. Turn on Pages

1. Repo → **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: **main**, folder: **/ (root)** → **Save**
4. Under **Custom domain**, type the domain you bought (no `https://`, no trailing slash) and save. GitHub commits a `CNAME` file to the repo for you. Use the apex — `reyaneantoine.com`, not `www.` — since step 3 points `www` at it.

First build takes a minute or two. The site is live at `https://agolden5.github.io/reyane-portfolio/` immediately, and at the custom domain once DNS propagates.

---

## 3. Namecheap DNS

Namecheap → **Domain List** → **Manage** → **Advanced DNS**.

Delete the parking records Namecheap adds by default (usually a `CNAME` for `www` → `parkingpage.namecheap.com` and a `URL Redirect` for `@`). Then add:

| Type | Host | Value | TTL |
|---|---|---|---|
| A | @ | 185.199.108.153 | Automatic |
| A | @ | 185.199.109.153 | Automatic |
| A | @ | 185.199.110.153 | Automatic |
| A | @ | 185.199.111.153 | Automatic |
| CNAME | www | agolden5.github.io. | Automatic |

Four A records, all four of them — GitHub load-balances across them.

The CNAME value is `agolden5.github.io` — your username only, **not** the repository name, and the trailing dot is fine.

Optional, and worth adding — these four AAAA records make the site reachable over IPv6:

| Type | Host | Value |
|---|---|---|
| AAAA | @ | 2606:50c0:8000::153 |
| AAAA | @ | 2606:50c0:8001::153 |
| AAAA | @ | 2606:50c0:8002::153 |
| AAAA | @ | 2606:50c0:8003::153 |

DNS usually resolves within 30 minutes but Namecheap allows up to 48 hours.

---

## 4. Turn on HTTPS

Once the domain resolves, go back to **Settings → Pages** and tick **Enforce HTTPS**. The checkbox stays greyed out until GitHub has issued the certificate, which can take up to an hour after DNS is correct. Don't share the link before this is on — an unencrypted portfolio looks careless to exactly the people she's sending it to.

---

## Updating the site later

Replace `index.html` (and `Reyane-Antoine-Portfolio.pdf` if it changed), then:

```bash
git add .
git commit -m "Update portfolio"
git push
```

Live in under a minute. Leave `.nojekyll` and the `CNAME` file GitHub created alone — deleting either breaks the custom domain.

---

## Troubleshooting

**404 at the custom domain, but the `.github.io` URL works.** DNS hasn't propagated, or the A records are wrong. Check with `dig reyaneantoine.com +short` — it should return the four `185.199.x.153` addresses.

**"Domain does not resolve to the GitHub Pages server."** Namecheap's parking records are still in place. Remove the `URL Redirect` record for `@`.

**The site loads but images don't.** Shouldn't happen — every image is embedded in `index.html` as data. If it does, the file was truncated in transfer; re-copy it.

**The PDF button 404s.** `Reyane-Antoine-Portfolio.pdf` didn't get committed. Check `git status` — large files are easy to miss if a `.gitignore` snuck in.
