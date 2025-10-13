Summarize pull request blockers across accessible repositories, unless a repo is specified here: $ARGUMENTS.

## Workflow

1. If a specific repository is provided in $ARGUMENTS (format: owner/repo), use `gh pr list --repo owner/repo --state open --limit 50 --json number,title,author,updatedAt,isDraft` to list PRs
2. Otherwise, use `gh search prs --involves @me --state open --json number,title,repository,author,updatedAt` to find PRs involving the authenticated user across all repositories
3. For each PR found, use `gh pr view <number> --repo owner/repo --json number,title,author,updatedAt,state,isDraft,mergeable,reviewDecision,statusCheckRollup,reviewRequests,comments,reviews,url` to fetch detailed status including checks, reviews, mergeable state, review conversations, and PR URL
4. Analyze each PR for blockers (failed checks, missing reviews, merge conflicts, etc. - exclude PR state as draft)
5. Sort PRs by most recent activity first
6. Report all blocked PRs with concise blocker summaries

## Blocker Detection

Check for these common blockers:
- **CI/CD failures** - Failed status checks or builds
- **Missing reviews** - No approving reviews or review requests pending
- **Requested changes** - Reviewers explicitly requesting changes
- **Merge conflicts** - Code conflicts with base branch
- **Unresolved conversations** - Review threads not marked resolved
- **Failed deployments** - Preview/staging deployment failures
- **Missing required checks** - Required status checks not completed
- **Stale review requests** - PRs with reviewers requested but no approving reviews for more than 1 business day (excluding weekends). To calculate business days: first get today's date, then count backward day-by-day from today, skipping weekends (Saturday/Sunday), until you reach the PR's updatedAt date. Count only the weekdays (Monday-Friday) in between. If it's been more than a week, simply say >1 week.

## Output

Report **only blocked PRs** (do not include unblocked PRs in the summary). For each blocked PR (ordered by most recent activity first): repository name, PR number, PR URL, title, author, last activity timestamp, and **concise blocker summary** (e.g., "Blocked by: failed CI, missing reviews (2 requested)", "Blocked by: changes requested by @username", "Blocked by: missing reviews from @reviewer1, @reviewer2")
