Prepare for a 1-on-1 meeting with a dev. Provide EMAIL (for Jira) and GH_USERNAME (for GitHub) here: $ARGUMENTS.

## Workflow

Follow these instructions explicitly:
1. Parse $ARGUMENTS to extract EMAIL and GH_USERNAME (space-separated, e.g., "user@example.com github-username")
2. Use `acli jira workitem search --json --jql "assignee = '{EMAIL}' and (sprint in openSprints() OR resolved >= -2w) ORDER BY resolved DESC" --fields "summary" --limit 50` to find their work items in current sprint or resolved in last 2 weeks
3. For each work item, use `acli jira workitem view {key}` to fetch detailed information including description, comments, and status
4. Use `gh search prs --author {GH_USERNAME} --updated ">={today - 14 days}" --json number,repository --limit 50` to find their recent PRs from the last 2 weeks
5. For each PR, use `gh pr view {number} --repo {owner/repo} --json number,title,author,updatedAt,state,isDraft,mergeable,reviewDecision,statusCheckRollup,reviewRequests,comments,reviews,url` to get detailed PR information
6. For each PR, analyze comments and reviews to identify if other users (with different usernames) assisted with the work - look for substantive code contributions, pair programming mentions, or significant technical guidance
7. Use `acli jira auth status` to get the "Site" value, which will be used to generate URLs later (to fill in {jira_site})
8. Organize and summarize the information for the 1-on-1 meeting

## Analysis

Provide insights on:
- **Completed work** - Work items resolved in the last 2 weeks
- **In-progress work** - Work items currently in progress in the sprint
- **Blockers** - Any items that appear blocked or stalled
- **PR status** - Draft PRs, open PRs, merged PRs, PR review feedback, PRs waiting on review
- **Quality signals** - Failed CI checks, requested changes, merge conflicts
- **Velocity** - Number of items completed vs in progress
- **Collaboration** - Other users who assisted with PRs (identified by their contributions in comments/reviews)

## Output

Organize the output into sections, remembering the following best practices:
- provide specific examples for any points made
- always provide URLs for Jira work items using the format: `https://{jira_site}/browse/{key}`
- always provide URLs for GitHub pull requests

### Recently Completed (Last 2 Weeks)
List resolved work items with keys, titles, and resolution dates

### Current Sprint Work
List in-progress work items with keys, titles, status, and any blockers or concerns

### Pull Requests
- **Draft/open PRs** - Include status checks, review status, and any issues
- **Recently Merged** - PRs merged in the last 2 weeks
- **Collaboration** - For each PR, note any other users who provided significant assistance (e.g., "with help from @username" if they provided substantive code suggestions, pair programming, or technical guidance)

## Dependencies

If this prompt errors on missing commands, provide the user with the links below:
- Atlassian CLI (acli): https://developer.atlassian.com/cloud/acli/guides/install-acli/
- GitHub CLI (gh): https://cli.github.com/