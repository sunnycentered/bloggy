# How-To Guide: GitHub Agent Workflow for Blog Management

## Overview
This guide walks you through setting up and using an automated blog publishing system powered by GitHub Agents. With minimal effort, you can submit blog posts via GitHub Issues, and the system will automatically validate, review, and deploy them to a live GitHub Pages site.

## Requirements

### Prerequisites
- **GitHub Account**: Free account at github.com
- **Git Installed**: For cloning and pushing repositories
- **Basic Markdown Knowledge**: For writing blog posts
- **GitHub Personal Access Token** (optional): For advanced agent features

### System Requirements
- **Repository Setup**: The blog-agentic-workflow repository (already created)
- **GitHub Pages Enabled**: For hosting the live blog
- **GitHub Actions Enabled**: For automated workflows (enabled by default)

### Tools Used
- **Jekyll**: Static site generator (installed via GitHub Actions)
- **Markdownlint**: For content validation
- **GitHub CLI** (optional): For advanced interactions

## Step 1: Set Up the Repository

### 1.1 Push to GitHub
```bash
# Navigate to your blog directory
cd /home/suncentered/hermes/blog-agentic-workflow

# Add GitHub remote (replace with your repo URL)
git remote add origin https://github.com/YOUR_USERNAME/blog-agentic-workflow.git

# Push to GitHub
git push -u origin master
```

### 1.2 Enable GitHub Pages
1. Go to your repository on GitHub
2. Click **Settings** tab
3. Scroll down to **Pages** section
4. Under **Source**, select **Deploy from a branch**
5. Choose **gh-pages** branch (it will be created automatically)
6. Click **Save**

Your blog will be live at: `https://YOUR_USERNAME.github.io/blog-agentic-workflow/`

## Step 2: Customize Your Blog

### 2.1 Update Configuration
Edit `_config.yml` to personalize:
```yaml
title: "Your Blog Name"
description: "Your blog description"
author: "Your Name"
```

### 2.2 Add Your First Post
The repository already includes an example post. You can modify it or create new ones.

## Step 3: Create a Blog Entry (Minimal Effort Method)

### Method 1: Via GitHub Issues (Recommended - Zero Local Setup)

1. **Go to Issues Tab** in your repository
2. **Click "New Issue"**
3. **Select "Submit Blog Post"** template
4. **Fill in the form**:
   - **Post Title**: "My First Blog Post"
   - **Post Content**: Write your markdown content
   - **Images**: Upload or link images if needed
5. **Add Label**: Click "Labels" and add "new-post"
6. **Submit Issue**

That's it! The agents will automatically:
- Process your submission
- Create a draft pull request
- Validate content
- Deploy when approved

### Method 2: Direct File Creation (For Advanced Users)

1. **Clone the repository locally**:
   ```bash
   git clone https://github.com/YOUR_USERNAME/blog-agentic-workflow.git
   cd blog-agentic-workflow
   ```

2. **Create a new post file**:
   ```bash
   # Create file with today's date
   TODAY=$(date +%Y-%m-%d)
   touch "_posts/${TODAY}-my-awesome-post.md"
   ```

3. **Edit the post** with your content:
   ```markdown
   ---
   layout: post
   title: "My Awesome Post"
   date: 2024-01-15
   author: "Your Name"
   tags: [tag1, tag2]
   categories: [category]
   ---

   # My Awesome Post

   This is my blog post content in Markdown.

   ## Section

   More content here...

   [Link to something](https://example.com)
   ```

4. **Commit and push**:
   ```bash
   git add _posts/
   git commit -m "Add new blog post: My Awesome Post"
   git push origin master
   ```

5. **Create Pull Request** for review (optional, but recommended)

## Step 4: Automated Deployment

### How It Works
1. **Content Submission**: Issue created or PR opened
2. **Validation**: Workflows check Markdown syntax, links, and content
3. **Review**: Automated checks run (currently basic, can be enhanced)
4. **Approval**: Manual approval or auto-approval based on checks
5. **Deployment**: Content merged and deployed to GitHub Pages

### Monitoring Progress
- **Actions Tab**: View workflow runs and logs
- **Pull Requests**: See review status and comments
- **Issues**: Track submission status

### Manual Deployment (If Needed)
If auto-deployment fails:
1. Go to **Actions** tab
2. Click **Deploy Blog** workflow
3. Click **Run workflow**
4. Select branch and run

## Step 5: Maintenance and Customization

### Updating Agents
The agents are in `.github/agents/`:
- `content-ingest.py`: Handles new submissions
- `review.py`: Validates content
- `deploy.py`: Manages deployment
- `maintenance.py`: Monitors site health

### Enhancing Workflows
Edit `.github/workflows/` files to add:
- Link checking with `curl`
- Image validation
- SEO checks
- Notification systems

### Adding Features
- **Comments**: Integrate Disqus or similar
- **Analytics**: Add Google Analytics
- **Themes**: Customize Jekyll theme
- **RSS Feed**: Auto-generate feed

## Troubleshooting

### Common Issues

**Workflows Not Running**
- Check Actions tab for errors
- Ensure repository has Actions enabled
- Verify YAML syntax in workflow files

**Pages Not Deploying**
- Check Pages settings in repository settings
- Ensure `gh-pages` branch exists
- Wait 5-10 minutes for deployment

**Content Not Validating**
- Check workflow logs for specific errors
- Ensure Markdown is properly formatted
- Verify links are accessible

**Agents Not Responding**
- Agents are currently placeholders
- Implement GitHub API calls for full automation
- Use webhooks for real-time processing

### Getting Help
- Check GitHub Actions documentation
- Review Jekyll documentation
- Examine workflow logs for detailed errors

## Quick Reference

### Commands
```bash
# Clone repository
git clone https://github.com/YOUR_USERNAME/blog-agentic-workflow.git

# Create new post
TODAY=$(date +%Y-%m-%d)
nano "_posts/${TODAY}-post-title.md"

# Push changes
git add . && git commit -m "Add post" && git push
```

### File Structure
```
blog-agentic-workflow/
├── _posts/           # Blog posts
├── _config.yml       # Site configuration
├── .github/
│   ├── workflows/    # Automation workflows
│   └── agents/       # Agent definitions
└── README.md         # This guide
```

### Labels
- `new-post`: Triggers content ingestion
- `needs-review`: Requires content validation
- `ready-to-deploy`: Approved for publishing

## Next Steps
1. Submit your first post using the Issues method
2. Watch the automation in action
3. Customize the theme and configuration
4. Enhance agents with real GitHub API integration
5. Add more advanced features like comments or search

Happy blogging! 🚀
