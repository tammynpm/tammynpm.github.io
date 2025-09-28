# tammynpm.github.io

This is a Hugo site configured for GitHub Pages using the Researcher theme and GitHub Actions to deploy to the `gh-pages` branch.

## Quick start

1. Create the GitHub repo named `tammynpm.github.io` (public recommended) under your `tammynpm` account.
2. Initialize git locally and push the code (commands below).
3. GitHub Actions will build and deploy to the `gh-pages` branch automatically.
4. In your repo settings, enable GitHub Pages to serve from the `gh-pages` branch (root).

## Local development (optional)
If you have Hugo installed locally, you can run:

```bash
hugo server -D
```

Then visit http://localhost:1313

## Deployment
This repo uses a GitHub Actions workflow at `.github/workflows/hugo.yaml` which will:
- Set up Hugo (extended)
- Resolve the Researcher theme via Hugo Modules
- Build the site into `public/`
- Publish to the `gh-pages` branch using the built-in `GITHUB_TOKEN`

## Customize
- Update site settings in `hugo.toml` (title, author, params, menu, etc.)
- Edit the homepage at `content/_index.md`
- Add posts under `content/posts/`
- Add images to `static/` and they will be served at the root (e.g., `/img.png`)

## Git commands to push initially
Replace the remote URL if you use SSH instead of HTTPS.

```bash
git init
git add .
git commit -m "Initial Hugo site with Researcher and GH Actions"
git branch -M main
git remote add origin https://github.com/tammynpm/tammynpm.github.io.git
git push -u origin main
```
