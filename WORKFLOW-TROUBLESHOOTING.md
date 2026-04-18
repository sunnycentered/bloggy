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

I've updated your `deploy.yml` workflow to use the `peaceiris/actions-gh-pages` action with proper permissions:

### 1. Added Explicit Permissions
```yaml
permissions:
  contents: write
```
This grants the workflow write access to push to the gh-pages branch.

### 2. Fixed Trigger Configuration
```yaml
on:
  push:
    branches:
      - main
  workflow_dispatch:
```
- **Removed** the `github-pages` environment protection (was causing deployment rejection)
- **Added** manual trigger option via `workflow_dispatch`

### 3. Used Reliable Deployment Action
- Switched back to `peaceiris/actions-gh-pages@v3` (battle-tested for Jekyll sites)
- Direct push to gh-pages branch
- No complex environment configuration needed

### 4. Improved Build Configuration
- Updated to Ruby/Jekyll best practices with `ruby/setup-ruby`
- Proper bundler caching for faster builds
- Direct Jekyll build command

## Additional Configuration Needed

### Step 1: Update Repository Settings
1. Go to your repository on GitHub
2. Click **Settings** → **Pages**
3. Under **Source**, select **Deploy from branch**
4. Choose **gh-pages** as the branch
5. Click **Save**

### Step 2: Enable Actions Permissions
1. Go to **Settings** → **Actions** → **General**
2. Under **Workflow permissions**, select:
   - ✅ Read and write permissions
   - ✅ Allow GitHub Actions to create and approve pull requests
3. Click **Save**

### Step 3: Create a Gemfile (Optional but Recommended)
Create a `Gemfile` in your repository root:
```ruby
source "https://rubygems.org"

gem "jekyll", "~> 4.3.0"

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.12"
  gem "jekyll-sitemap"
  gem "jekyll-seo-tag"
end
```

Then run locally:
```bash
bundle install
```

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
| **Permissions** | None | Explicit write permissions |
| **Deploy Action** | peaceiris/actions-gh-pages (old) | peaceiris/actions-gh-pages (fixed) |
| **Setup** | Manual apt-get | Ruby/Bundler via actions |
| **Build** | Conditional logic | Direct Jekyll build |
| **Environment** | None | Removed github-pages environment |

## Next Steps

1. ✅ Push the updated workflow
2. ✅ Update repository permissions settings
3. ✅ Manually trigger workflow to test
4. ✅ Verify site is live
5. ✅ Create a test blog post via Issues

Your workflow should now deploy successfully! 🚀
