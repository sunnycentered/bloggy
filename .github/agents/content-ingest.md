# Content Ingest Agent
This agent:
1. Monitors repository issues for new post submissions
2. Extracts post content and metadata (title, date, tags)
3. Creates draft pull request for review
4. Notifies author via issue comment
Triggers:
- New issue with "new-post" label
- Webhook from issue creation
Dependencies:
- GitHub API Access Token
- Repository permissions: issues, pull_requests, write
