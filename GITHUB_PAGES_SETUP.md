# GitHub Pages Setup Guide

This document provides step-by-step instructions for completing the GitHub Pages setup for the custom domain **builder.jobbyist.co.za**.

## Overview

The repository is now configured to automatically build and deploy to GitHub Pages using GitHub Actions. The workflow is triggered on every push to the `main` branch or can be manually triggered.

## Prerequisites Completed ✅

- ✅ GitHub Actions workflow configured (`.github/workflows/deploy.yml`)
- ✅ CNAME file configured with custom domain (`public/CNAME`)
- ✅ Build process tested and working
- ✅ Documentation updated

## Required Setup Steps

### 1. Configure GitHub Repository Secrets

The build process requires Supabase environment variables to be configured as GitHub Secrets.

**Steps:**
1. Go to your GitHub repository
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret** and add each of the following:

| Secret Name | Value | Description |
|------------|-------|-------------|
| `VITE_SUPABASE_URL` | `https://qwpegrmoqkgtioddyonv.supabase.co` | Supabase project URL |
| `VITE_SUPABASE_PUBLISHABLE_KEY` | (get from `.env` file) | Supabase publishable/anon key |
| `VITE_SUPABASE_PROJECT_ID` | `qwpegrmoqkgtioddyonv` | Supabase project ID |

**To get the values:**
- Check the `.env` file in the repository (not committed to git)
- Or get them from the Supabase dashboard at https://supabase.com/dashboard

### 2. Enable GitHub Pages

**Steps:**
1. Go to your GitHub repository
2. Navigate to **Settings** → **Pages**
3. Under **Build and deployment**:
   - Source: Select **GitHub Actions**
4. Click **Save**

The custom domain should automatically be detected from the CNAME file.

### 3. Configure DNS

You need to configure your DNS provider to point `builder.jobbyist.co.za` to GitHub Pages.

#### Option A: Using CNAME Record (Recommended)

Add a CNAME record in your DNS provider:

```
Type: CNAME
Name: builder
Value: jobbyist.github.io
TTL: 3600 (or your preferred value)
```

#### Option B: Using A Records

If you prefer A records, add the following records:

```
Type: A
Name: builder
Value: 185.199.108.153
TTL: 3600

Type: A
Name: builder
Value: 185.199.109.153
TTL: 3600

Type: A
Name: builder
Value: 185.199.110.153
TTL: 3600

Type: A
Name: builder
Value: 185.199.111.153
TTL: 3600
```

**Note:** DNS changes can take up to 24-48 hours to propagate, but typically take effect within a few hours.

### 4. Verify GitHub Pages Custom Domain

After DNS is configured:

1. Go to **Settings** → **Pages** in your GitHub repository
2. In the **Custom domain** section, you should see `builder.jobbyist.co.za`
3. If the domain is not automatically detected, enter it manually and click **Save**
4. Wait for the DNS check to complete (GitHub will verify the DNS configuration)
5. Once verified, you can optionally enable **Enforce HTTPS** (recommended)

## Testing the Deployment

### Manual Workflow Trigger

To test the deployment immediately:

1. Go to the **Actions** tab in your GitHub repository
2. Select the **Deploy to GitHub Pages** workflow
3. Click **Run workflow**
4. Select the `main` branch
5. Click **Run workflow**

The workflow will:
1. Checkout the code
2. Set up Node.js 20
3. Install dependencies with `npm ci`
4. Build the project with `npm run build` (using the configured secrets)
5. Upload the build artifacts
6. Deploy to GitHub Pages

### Automatic Deployment

Once everything is configured, the site will automatically deploy whenever you push changes to the `main` branch.

## Workflow Details

The deployment workflow (`.github/workflows/deploy.yml`) includes:

- **Trigger**: Push to `main` branch or manual workflow dispatch
- **Permissions**: Read contents, write to pages, use id-token
- **Build Step**: 
  - Uses Node.js 20
  - Runs `npm ci` to install dependencies
  - Runs `npm run build` with Supabase environment variables
  - Uploads `./dist` directory as artifact
- **Deploy Step**:
  - Deploys the artifact to GitHub Pages
  - Outputs the deployment URL

## Verification

After deployment completes:

1. Check the Actions tab for successful workflow runs
2. Visit `https://builder.jobbyist.co.za` (after DNS propagates)
3. Verify that the site loads correctly
4. Check that all functionality works (especially Supabase integration)

## Troubleshooting

### Build Fails with Missing Environment Variables

**Problem:** Build fails with errors about missing Supabase configuration.

**Solution:** Ensure all three secrets are configured correctly in the repository settings:
- `VITE_SUPABASE_URL`
- `VITE_SUPABASE_PUBLISHABLE_KEY`
- `VITE_SUPABASE_PROJECT_ID`

### Custom Domain Not Working

**Problem:** Visiting `builder.jobbyist.co.za` doesn't load the site.

**Solution:** 
1. Verify DNS configuration with: `dig builder.jobbyist.co.za` or `nslookup builder.jobbyist.co.za`
2. Ensure DNS records point to `jobbyist.github.io` or GitHub Pages IP addresses
3. Wait for DNS propagation (can take up to 48 hours)
4. Check GitHub Pages settings to ensure custom domain is properly configured

### HTTPS Not Working

**Problem:** Site loads but shows "Not Secure" warning.

**Solution:**
1. In GitHub Pages settings, ensure DNS check has completed successfully
2. Enable "Enforce HTTPS" option
3. Wait a few minutes for GitHub to provision an SSL certificate
4. Clear browser cache and try again

### Deployment Succeeds but Site Not Updated

**Problem:** Deployment shows success but site shows old content.

**Solution:**
1. Clear browser cache and hard refresh (Ctrl+Shift+R or Cmd+Shift+R)
2. Check the workflow run to ensure it completed all steps
3. Verify the correct branch was deployed
4. Check if there's a CDN caching issue

## Monitoring

To monitor deployments:

1. Go to the **Actions** tab to see workflow runs
2. Click on a specific run to see detailed logs
3. Check the **Environments** section in the repository to see deployment history

## Additional Resources

- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Configuring Custom Domain](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Vite Production Build](https://vitejs.dev/guide/build.html)

## Support

If you encounter issues not covered in this guide:

1. Check the Actions tab for detailed error logs
2. Review the workflow file: `.github/workflows/deploy.yml`
3. Verify DNS configuration with online DNS lookup tools
4. Check GitHub Pages status at [https://www.githubstatus.com/](https://www.githubstatus.com/)

## Summary

Once all steps are completed:

- ✅ Secrets configured in GitHub
- ✅ GitHub Pages enabled with GitHub Actions source
- ✅ DNS configured to point to GitHub Pages
- ✅ Custom domain verified in GitHub Pages settings
- ✅ HTTPS enforced (optional but recommended)

Your resume builder application will be available at **https://builder.jobbyist.co.za** and will automatically deploy on every push to `main`.
