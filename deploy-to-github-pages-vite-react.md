# Deploy a Vite + React App to GitHub Pages (Terminal Guide)

This guide shows how to deploy a **Vite + React** project to **GitHub Pages** using only the terminal (plus one quick toggle in the GitHub UI). Keep this file in your repo (e.g., `docs/DEPLOY.md`) for future reference.

---

## Prerequisites

- Node.js and npm installed.
- A GitHub account and a repository to host your app.
- Your local project is a Vite + React app (created via `npm create vite@latest` or similar).
- Your repo is connected to GitHub (`git remote -v` shows an `origin`).

> Example repo used below: **`learning_react`** owned by **`YOUR_GITHUB_USERNAME`**.

---

## 1) Install the deploy tool

```bash
npm install --save-dev gh-pages
```

This adds the `gh-pages` CLI used to publish the built site to a `gh-pages` branch.

---

## 2) Set the base path in Vite

For **project pages** (site served at `https://YOUR_GITHUB_USERNAME.github.io/REPO_NAME/`), set `base` to `"/REPO_NAME/"` (note the **trailing slash**).

Edit **`vite.config.js`** (or `vite.config.ts`):

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  base: '/learning_react/', // 👈 replace with your repo name, keep the trailing slash!
  plugins: [react()],
})
```

- If you’re deploying to a **user/organization page** at `https://YOUR_GITHUB_USERNAME.github.io/` (no repo segment), use `base: '/'` instead.

---

## 3) Add deploy scripts to `package.json`

Make sure you have a build script (Vite’s default is `vite build`). Then add `predeploy` and `deploy`:

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "predeploy": "npm run build",
    "deploy": "gh-pages -d dist"
  }
}
```

- `predeploy` runs automatically before `deploy` to build your app into the `dist/` folder.
- `deploy` publishes the `dist/` folder to the `gh-pages` branch.

---

## 4) Commit your changes

```bash
git add vite.config.* package.json package-lock.json
git commit -m "chore: configure GitHub Pages (gh-pages + base path)"
git push origin main
```

> Replace `main` if your default branch is `master` or something else.

---

## 5) Deploy from the terminal

```bash
npm run deploy
```

You should see output indicating that the `gh-pages` branch was pushed to GitHub. This creates/updates the branch used by GitHub Pages.

---

## 6) Enable GitHub Pages (one-time per repo)

1. Go to your repository on GitHub → **Settings** → **Pages**.
2. **Source**: choose **Deploy from a branch**.
3. **Branch**: select **`gh-pages`** (root) → **Save**.

GitHub will publish your site shortly at:

```
https://YOUR_GITHUB_USERNAME.github.io/learning_react/
```

> Publishing can take a couple of minutes on the first run.

---

## 7) Verify your site

Open your browser to:

```
https://YOUR_GITHUB_USERNAME.github.io/learning_react/
```

You should see your app. If assets don’t load or you get a blank page, double‑check the `base` setting and the URL path.

---

## 8) Redeploy after changes

Anytime you modify code:

```bash
git add -A
git commit -m "feat: update UI and behavior"
git push origin main
npm run deploy
```

This rebuilds and republishes the `dist/` folder to GitHub Pages.

---

## Example: Minimal working setup

### `vite.config.js`
```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  base: '/learning_react/',
  plugins: [react()],
})
```

### `package.json` (snippet)
```json
{
  "name": "learning_react",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "predeploy": "npm run build",
    "deploy": "gh-pages -d dist"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.3.0",
    "gh-pages": "^6.0.0",
    "vite": "^5.0.0"
  }
}
```

---

## Optional: SPA routing fix (React Router)

For single‑page apps using client‑side routing, direct‑linking to nested routes can 404 on GitHub Pages. A simple workaround is to serve `index.html` for unknown paths via a `404.html` copy:

Add a post‑build script (macOS/Linux):

```json
{
  "scripts": {
    "predeploy": "npm run build && cp dist/index.html dist/404.html",
    "deploy": "gh-pages -d dist"
  }
}
```

On Windows (PowerShell):

```json
{
  "scripts": {
    "predeploy": "npm run build && copy dist\index.html dist\404.html",
    "deploy": "gh-pages -d dist"
  }
}
```

---

## Troubleshooting

- **Blank page / missing CSS/JS**: Ensure `base` is `'/REPO_NAME/'` with a trailing slash.
- **404 on refresh for nested routes**: Use the SPA routing fix above.
- **Wrong URL**: Project pages live at `https://YOUR_GITHUB_USERNAME.github.io/REPO_NAME/`.
- **Cache issues**: Hard refresh or open in a private window after deploys.
- **Private repos**: GitHub Pages requires appropriate settings/plan; most project pages work best on public repos.

---

## One‑liner quickstart (after initial config)

```bash
npm i -D gh-pages && git add -A && git commit -m "setup gh-pages" && git push && npm run deploy
```

---

### Summary

1. `npm i -D gh-pages`
2. Set `base` in `vite.config.*`
3. Add `predeploy`/`deploy` scripts
4. `git add && git commit && git push`
5. `npm run deploy`
6. Enable **Settings → Pages → Branch: gh-pages**
7. Visit `https://YOUR_GITHUB_USERNAME.github.io/REPO_NAME/`

Happy shipping! 🚀
