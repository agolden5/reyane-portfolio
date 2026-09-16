# Reyane Antoine — portfolio site

Everything needed to host the portfolio at a custom domain, free, on GitHub Pages.

| File | What it is |
|---|---|
| `index.html` | The whole site. Fonts come from Google; every image is embedded, so there are no other assets to upload. |
| `Reyane-Antoine-Portfolio.pdf` | The attachable version. The "Download portfolio (PDF)" buttons on the page point at this file, so it has to sit next to `index.html`. |
| `Reyane-Antoine-Resume.pdf` | The standalone résumé download. |
| `.nojekyll` | Tells GitHub to serve the files as-is instead of running them through Jekyll. |
| `CNAME` | Created automatically by GitHub Pages once a custom domain is set (see step 3 below) — don't delete it. |

---

## 1. Create the repo and push

From inside this folder.

```bash
git init -b main
git add .
git commit -m "Portfolio site"
git remote add origin <your-repo-url>
git push -u origin main
```

Create the repo first at https://github.com/new, **Public** (Pages needs public on a free account), and do **not** tick "Add a README", .gitignore, or license, or the push above gets rejected.

With the `gh` CLI installed and signed in, this does all of it in one line, repo creation included:

```bash
gh repo create <repo-name> --public --source=. --remote=origin --push
```

---

## 2. Turn on Pages

1. Repo → **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: **main**, folder: **/ (root)** → **Save**
4. Under **Custom domain**, type the domain you bought (no `https://`, no trailing slash) and save. GitHub commits a `CNAME` file to the repo for you. Use the apex domain, not `www.`, since step 3 points `www` at it.

First build takes a minute or two. The site is live at the `github.io` URL immediately, and at the custom domain once DNS propagates.

---

## 3. DNS (registrar-side)

At your domain registrar's DNS settings, delete any parking records and add:

| Type | Host | Value | TTL |
|---|---|---|---|
| A | @ | 185.199.108.153 | Automatic |
| A | @ | 185.199.109.153 | Automatic |
| A | @ | 185.199.110.153 | Automatic |
| A | @ | 185.199.111.153 | Automatic |
| CNAME | www | `<your-github-username>.github.io.` | Automatic |

Four A records, all four of them — GitHub load-balances across them.

The CNAME value is your GitHub username, **not** the repository name (trailing dot is fine).

Optional, and worth adding — these four AAAA records make the site reachable over IPv6:

| Type | Host | Value |
|---|---|---|
| AAAA | @ | 2606:50c0:8000::153 |
| AAAA | @ | 2606:50c0:8001::153 |
| AAAA | @ | 2606:50c0:8002::153 |
| AAAA | @ | 2606:50c0:8003::153 |

DNS usually resolves within 30 minutes but can take up to 48 hours depending on the registrar.

---

## 4. Turn on HTTPS

Once the domain resolves, go back to **Settings → Pages** and tick **Enforce HTTPS**. The checkbox stays greyed out until GitHub has issued the certificate, which can take up to an hour after DNS is correct. Don't share the link before this is on — an unencrypted portfolio looks careless to the people it's being sent to.

---

## Updating the site later

Replace `index.html` (and the PDFs, if they changed), then:

```bash
git add .
git commit -m "Update portfolio"
git push
```

Live in under a minute. Leave `.nojekyll` and `CNAME` alone — deleting either breaks the custom domain.

---

## Troubleshooting

**404 at the custom domain, but the `.github.io` URL works.** DNS hasn't propagated, or the A records are wrong. Check with `dig <yourdomain> +short` — it should return the four `185.199.x.153` addresses.

**"Domain does not resolve to the GitHub Pages server."** The registrar's default parking records are still in place. Remove any `URL Redirect` record for `@`.

**The site loads but images don't.** Shouldn't happen — every image is embedded in `index.html` as data. If it does, the file was truncated in transfer; re-copy it.

**The PDF button 404s.** The PDF didn't get committed. Check `git status` — large files are easy to miss if a `.gitignore` snuck in.
