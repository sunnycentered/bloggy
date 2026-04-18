# GitHub Workflow Permissions Fix - Troubleshooting Guide

## Problem
You received a 403 permission denied error when GitHub Actions tried to push to gh-pages:
```
remote: Permission to sunnycentered/bloggy.git denied to github-actions[bot].
fatal: unable to access 'https://github.com/sunnycentered/bloggy.git/': The requested URL returned error: 403
```

## Root Causes
1. **Missing Permissions**: The workflow didn't have explicit write permissions
2. **Circular Trigger**: Workflow was triggered by pushes to BOTH main and gh-pages, causing conflicts
3. **Outdated Action**: Using `peaceiris/actions-gh-pages` instead of GitHub's native `actions/deploy-pages`

## Solution Applied

I've updated your `deploy.yml` workflow to use **GitHub's native Pages deployment** with proper configuration:

### 1. Added Required Permissions
```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```
These are the exact permissions required for native GitHub Pages deployment.

### 2. Separated Build and Deploy Jobs
- **Build Job**: Compiles the Jekyll site and uploads as artifact
- **Deploy Job**: Deploys the artifact to GitHub Pages (runs only after build succeeds)

### 3. Used Native GitHub Actions
- `actions/upload-pages-artifact@v3`: Official artifact upload for Pages
- `actions/deploy-pages@v4`: Official Pages deployment action
- Proper environment configuration with `github-pages` environment

### 4. Added Concurrency Control
```yaml
concurrency:
  group: "pages"
  cancel-in-progress: false
```
Prevents multiple deployments from running simultaneously.

## Additional Configuration Needed

### Step 1: Enable GitHub Pages with Actions
1. Go to your repository on GitHub
2. Click **Settings** → **Pages**
3. Under **Source**, select **GitHub Actions**
4. Click **Save**

**Important**: This is different from "Deploy from branch" - you must select "GitHub Actions" for native deployment.

### Step 2: Verify Actions Permissions
1. Go to **Settings** → **Actions** → **General**
2. Under **Workflow permissions**, ensure:
   - ✅ Read and write permissions (recommended)
3. Click **Save**

### Step 3: Check Environment Settings (Optional)
The `github-pages` environment is created automatically, but you can customize it:
1. Go to **Settings** → **Environments**
2. Click **github-pages**
3. Configure deployment branches or add reviewers if needed
4. By default, it should allow deployments from the workflow

## Testing the Fix

1. **Commit the updated workflow**:
   ```bash
   git add .github/workflows/deploy.yml
   git commit -m "Fix GitHub Actions permissions for deployment"
   git push origin main
   ```

2. **Trigger the workflow manually**:
   - Go to **Actions** tab
   - Click **Deploy Blog**
   - Click **Run workflow**
   - Select **main** branch
   - Click green **Run workflow** button

3. **Monitor the deployment**:
   - Watch the workflow run in real-time
   - Check the logs for any errors
   - Once complete, verify your site is live at `https://sunnycentered.github.io/bloggy/`

## Troubleshooting If Still Failing

### Issue: Still Getting Permission Denied
**Solution**: Check GitHub Actions settings
- Settings → Actions → General
- Ensure "Workflow permissions" is set to "Read and write permissions"

### Issue: 404 on GitHub Pages
**Solution**: Verify Pages deployment
- Settings → Pages
- Ensure Branch is set to **gh-pages** (created automatically by workflow)
- Wait 1-2 minutes for initial deployment

### Issue: Empty gh-pages Branch
**Solution**: The workflow hasn't run successfully yet
- Check Actions tab for workflow runs
- Look for error messages in the logs
- Re-run the workflow manually

### Issue: Ruby/Bundle Errors
**Solution**: Create a Gemfile with correct gems
- Ensure your `_config.yml` has no syntax errors
- Test locally with `bundle exec jekyll build`
- Check for Jekyll plugin compatibility

## Verification Steps

After deployment, verify everything works:

```bash
# 1. Check that gh-pages branch was created
git branch -a

# 2. Verify your site builds locally
bundle install
bundle exec jekyll build

# 3. View site locally
bundle exec jekyll serve
# Visit http://localhost:4000
```

## Key Changes Made to deploy.yml

| Aspect | Before | After |
|--------|--------|-------|
| **Trigger** | main + gh-pages (circular) | main only + manual |
| **Permissions** | None | contents: read, pages: write, id-token: write |
| **Deploy Action** | peaceiris/actions-gh-pages (broken) | actions/deploy-pages@v4 (native) |
| **Setup** | Manual apt-get | Ruby/Bundler via actions |
| **Build** | Conditional logic | Separated build/deploy jobs |
| **Environment** | None | github-pages environment with protection |
| **Concurrency** | None | Pages concurrency control |

## Next Steps

1. ✅ Push the updated workflow
2. ✅ Update repository permissions settings
3. ✅ Manually trigger workflow to test
4. ✅ Verify site is live
5. ✅ Create a test blog post via Issues

Your workflow should now deploy successfully! 🚀
