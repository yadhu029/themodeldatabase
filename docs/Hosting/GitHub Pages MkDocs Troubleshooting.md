# GitHub Pages + MkDocs Troubleshooting: From 404 to Fully Themed

> You’ve got a repo with `mkdocs.yml`, `docs/`, and a GitHub Pages workflow… but live you see **404**, a **directory listing**, or a **plain Markdown-looking page** (no Material theme). Here’s a practical, copy-paste guide that fixes the common pitfalls fast.

---

## TL;DR — Fix Checklist

1. **Use a MkDocs deploy workflow (not Jekyll).**
2. **Build to `site/` and upload that folder** as the Pages artifact.
3. **Publish a `CNAME`** in the artifact if you use a custom domain.
4. **Include `.nojekyll`** so Pages doesn’t treat it like a Jekyll site.
5. **Set `site_url:`** in `mkdocs.yml` to your final domain.
6. **Install your theme/plugins in CI** (e.g., `mkdocs-material`).
7. **Make sure `docs/index.md` exists** (so you get `site/index.html`).
8. **Trigger a fresh deploy** after fixing Git divergence (`git pull --rebase`, then push).

---

## Symptoms → Likely Cause → Fix

### 1) **404 Not Found** on your domain

* **Cause:** No successful deploy has reached Pages; or custom domain isn’t bound via `CNAME`.
* **Fix:**

  * Ensure your workflow finished green and uploaded `site/` with `index.html`.
  * Add `CNAME` to the artifact (see template below) or set `with: cname:` in the deploy step.
  * Settings → Pages → Source = **GitHub Actions**.

---

### 2) “**Index of …**” directory listing

* **Cause:** You’re viewing a local `file:///…` path or browsing a folder without `index.html`.
* **Fix:**

  * Use `mkdocs serve` locally, or `python3 -m http.server -d site 9000` after `mkdocs build`.
  * For a subpath like `/yum/`, add `docs/yum/index.md` so MkDocs emits `site/yum/index.html`.

---

### 3) Live site looks like **plain HTML** (no theme, no nav/search)

* **Cause:** CSS/JS assets 404 (wrong base URL) **or** a Jekyll workflow overwrote your MkDocs build.
* **Fix:**

  * Set `site_url: https://your.domain/` in `mkdocs.yml`.
  * Remove any Jekyll deploy workflow.
  * In CI, **install Material** (and any plugins) before `mkdocs build`.
  * Ensure **artifact uploads `./site`** (which contains `assets/…`).

---

### 4) **Push rejected** (`fetch first`)

* **Cause:** Your local branch is behind GitHub.
* **Fix:**

  ```bash
  git switch main
  git fetch origin
  git pull --rebase origin main
  # resolve if needed:
  git rebase --continue
  git push origin main
  ```

  (Re-run the workflow with an empty commit if needed: `git commit --allow-empty -m "Trigger deploy" && git push`.)

---

## Known-Good MkDocs Pages Workflow (drop-in)

Place as `.github/workflows/mkdocs.yml` and replace `your.domain`:

```yaml
name: Deploy MkDocs to GitHub Pages
on:
  push: { branches: [ main ] }
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency: { group: pages, cancel-in-progress: false }

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: '3.x' }

      - name: Install MkDocs + Material
        run: |
          python -m pip install --upgrade pip
          pip install mkdocs mkdocs-material  # add any plugins you list in mkdocs.yml

      - name: Build site
        run: mkdocs build --strict

      - name: Add .nojekyll & CNAME
        run: |
          touch site/.nojekyll
          echo your.domain > site/CNAME

      - name: Sanity check build output
        run: |
          test -f site/index.html || (echo "index.html missing" && exit 1)
          test -d site/assets || (echo "assets/ missing" && ls -la site && exit 1)
          find site -maxdepth 2 -type f | sed -n '1,60p'

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with: { path: ./site }

  deploy:
    runs-on: ubuntu-latest
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy
        id: deployment
        uses: actions/deploy-pages@v4
        with:
          cname: your.domain
```

> In **Settings → Pages**, set **Source = GitHub Actions**.

---

## Minimal `mkdocs.yml` that “just works”

```yaml
site_name: Your Site
site_url: https://your.domain/
use_directory_urls: true

theme:
  name: material
  logo: assets/logo.png    # file at docs/assets/logo.png
plugins:
  - search

markdown_extensions:
  - toc:
      permalink: true

validation:
  links:
    absolute_links: relative_to_docs
    anchors: warn
    unrecognized_links: warn
```

**Notes**

* Place content under `docs/` (e.g., `docs/index.md`, `docs/yum/index.md`).
* Ensure the logo path is relative to `docs/`.

---

## Deep-Dive Debugging (fast)

1. **View Source** on the live site. Look for:

   * `meta name="generator" content="mkdocs-...":` ✅ MkDocs build
   * If missing and the page is bare → wrong pipeline (likely Jekyll) or missing assets.

2. **DevTools → Network**
   Reload. Any 404s for:

   * `assets/stylesheets/*.css`
   * `assets/javascripts/*.js`
     If 404 → fix `site_url:` and ensure `assets/` is in `site/` and uploaded.

3. **Check the artifact in CI**
   The upload step must point to `./site` and include the files above. Add a `find site` step (as in the template).

4. **Reproduce production locally**

   ```bash
   mkdocs build --strict
   python3 -m http.server -d site 9000
   # open http://127.0.0.1:9000
   ```

   If this looks correct, your build is fine; focus on CI upload/deploy settings.

---

## Common Gotchas (and fixes)

| Problem               | Why it happens                                       | Fix                                                                             |
| --------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------- |
| 404 on custom domain  | No `CNAME` in artifact / no successful deploy        | Write `site/CNAME` in CI or `with: cname:`; ensure run is green                 |
| Directory listing     | No `index.html` in that folder                       | Create `docs/section/index.md` so MkDocs emits it                               |
| Site looks plain      | CSS/JS 404 or Jekyll overwrite                       | Install Material in CI, set `site_url:`, remove Jekyll workflow, upload `site/` |
| Broken relative links | Wrong `site_url` or absolute paths in `extra_css/js` | Use `site_url` and **relative** paths (`stylesheets/extra.css`)                 |
| CI build fails        | Missing theme/plugin in CI                           | `pip install mkdocs-material` (and your plugins)                                |
| Push rejected         | Local behind remote                                  | `git pull --rebase origin main` then push                                       |

---

## Post-Go-Live Polish

* **Pin deps**: add a `requirements.txt` and install from it in CI.
* **Custom 404**: create `docs/404.md` to avoid ugly fallbacks.
* **Favicon & repo links**: set `favicon:` and `repo_url:`/`edit_uri:` in `mkdocs.yml`.
* **Cache pip** in CI for speed.

---

### Final sanity loop

* ✅ One active Pages workflow (MkDocs).
* ✅ Artifact uploads **`./site`** with **`index.html`**, **`assets/`**, **`CNAME`**, **`.nojekyll`**.
* ✅ `site_url:` matches your domain.
* ✅ No 404s for CSS/JS in DevTools.


