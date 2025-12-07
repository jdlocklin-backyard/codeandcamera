# GitHub Actions Deployment Guide

This project uses GitHub Actions to automatically build and deploy the MkDocs site to GitHub Pages.

## How It Works

The CI/CD pipeline (`.github/workflows/ci.yml`) is triggered on every push to `main` or `master` branches and:

1. Checks out the repository code
2. Sets up Python environment
3. Caches dependencies for faster builds
4. Installs mkdocs-material
5. Builds the documentation
6. Deploys to GitHub Pages

## Updating the Site

Simply commit and push your changes to the `main` branch:

```bash
git add .
git commit -m "Your commit message"
git push origin main
```

The workflow will automatically:
- Build the latest documentation
- Deploy it to GitHub Pages at `https://codeandcamera.me/`

No manual deployment steps required.

## Monitoring Deployment

View deployment status in GitHub:
1. Go to your repository
2. Click the **Actions** tab
3. Select the latest workflow run to see build logs

## GitHub Pages Configuration

Ensure your repository settings have:
- **Pages** source set to deploy from `gh-pages` branch (created automatically by the workflow)
- Custom domain pointing to your site (if using a custom domain)
