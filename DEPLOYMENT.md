# Deployment Guide

This document outlines the steps to deploy your Zola site to GitHub Pages with a custom domain.

## Prerequisites

- Git repository initialized locally (already done)
- GitHub account
- Domain waifod.dev with DNS access

## Step 1: Create GitHub Repository

1. Go to [GitHub](https://github.com) and log in
2. Click the "+" icon in the top right and select "New repository"
3. Configure the repository:
   - Repository name: Choose a name (e.g., `waifod-site`)
   - Visibility: Public (required for GitHub Pages on free tier)
   - Do NOT initialize with README, .gitignore, or license (we already have these)
4. Click "Create repository"

## Step 2: Connect Local Repository to GitHub

After creating the repository, GitHub will show you commands. Run these in your local repository:

```bash
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git branch -M main
git push -u origin main
```

Replace `YOUR_USERNAME` and `YOUR_REPO_NAME` with your actual GitHub username and repository name.

## Step 3: Configure GitHub Actions Permissions

The GitHub Actions workflow needs permission to deploy to the gh-pages branch:

1. Go to your repository on GitHub
2. Click "Settings" tab
3. In the left sidebar, click "Actions" → "General"
4. Scroll down to "Workflow permissions"
5. Select "Read and write permissions"
6. Check "Allow GitHub Actions to create and approve pull requests"
7. Click "Save"

## Step 4: Configure GitHub Pages

1. In your repository settings, click "Pages" in the left sidebar
2. Under "Source", select "Deploy from a branch"
3. Under "Branch", select:
   - Branch: `gh-pages`
   - Folder: `/ (root)`
4. Click "Save"

Note: The gh-pages branch will be created automatically by the GitHub Actions workflow on the first deployment.

## Step 5: Add Custom Domain

1. Still in the Pages settings, find the "Custom domain" section
2. Enter: `waifod.dev`
3. Click "Save"
4. Wait for DNS check to complete (this may take a few minutes)
5. Once DNS check passes, check "Enforce HTTPS"

## Step 6: Configure DNS

At your domain registrar (where you purchased waifod.dev):

1. Add a CNAME record:
   - Type: `CNAME`
   - Name: `@` or `waifod.dev` (depending on your registrar)
   - Value: `YOUR_USERNAME.github.io`
   - TTL: 3600 (or default)

2. If your registrar doesn't support CNAME for apex domains, use A records instead:
   - Type: `A`
   - Name: `@`
   - Value: Add all four GitHub Pages IP addresses:
     - `185.199.108.153`
     - `185.199.109.153`
     - `185.199.110.153`
     - `185.199.111.153`

3. DNS propagation can take up to 24-48 hours, but usually completes within a few hours

## Step 7: Verify Deployment

1. Push a commit to the main branch:
   ```bash
   git add .
   git commit -m "feat: trigger initial deployment"
   git push
   ```

2. Go to the "Actions" tab in your GitHub repository
3. You should see the "Deploy to GitHub Pages" workflow running
4. Wait for it to complete (usually takes 1-2 minutes)
5. Once complete, visit `https://waifod.dev` to see your site

## Troubleshooting

### Workflow fails with permission error
- Verify you've enabled "Read and write permissions" in Actions settings (Step 3)

### Custom domain shows 404
- Wait for DNS propagation (can take up to 48 hours)
- Verify CNAME record is correctly configured
- Check that GitHub Pages is configured to use gh-pages branch

### Site not updating after push
- Check the Actions tab for workflow status
- Review workflow logs for any errors
- Ensure the workflow file is in `.github/workflows/deploy.yml`

### HTTPS not available
- Wait for DNS to fully propagate
- Ensure "Enforce HTTPS" is checked in Pages settings
- GitHub may take a few minutes to provision the SSL certificate

## Maintenance

After initial setup, the deployment process is automatic:

1. Make changes to your content or configuration locally
2. Commit the changes: `git commit -m "your message"`
3. Push to GitHub: `git push`
4. GitHub Actions will automatically build and deploy your site
5. Changes will be live at waifod.dev within 1-2 minutes
