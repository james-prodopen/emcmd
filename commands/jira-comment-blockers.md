Summarize work item blockers in the current sprint. Optionally provide a PROJECT_KEY here: $ARGUMENTS.

## Dependencies

Ensure that acli is installed: https://developer.atlassian.com/cloud/acli/guides/install-acli/

## Workflow

1. If a PROJECT_KEY is provided in $ARGUMENTS, use `acli jira workitem search --json --jql "project = {PROJECT_KEY} AND sprint IN openSprints() and status != Done ORDER BY updated ASC" --fields "summary" --limit 50` to search for work items in that project
2. Otherwise, use `acli jira workitem search --json --jql "sprint IN openSprints() and status != Done ORDER BY updated ASC" --fields "summary" --limit 50` to find work items in the current sprint that aren't done
3. For each work item found, use `acli jira workitem comment list --key {KEY}` to fetch all comments on the work item
4. Analyze each work item's comments for mentions of blockers
6. Report all blocked work items with concise blocker summaries

## Blocker Detection

Check for these common blocker indicators in comments:
- **Explicit blocker mentions** - Comments containing words like "blocked", "blocker", "blocking", "can't proceed"
- **Dependency issues** - Waiting on other teams, external dependencies, or upstream work
- **Technical blockers** - Infrastructure issues, environment problems, access issues
- **Resource blockers** - Waiting for design, waiting for requirements, waiting for product management, waiting for approval
- **Third-party blockers** - Vendor delays, API issues, external service problems
- **Review blockers** - Waiting for code review, waiting for QA, waiting for stakeholder approval

## Output

Report **only blocked work items** (do not include unblocked items in the summary). For each blocked item: work item key, summary, and **concise blocker summary** extracted from comments (e.g., "Blocked by: waiting on API access from Platform team", "Blocked by: infrastructure issues in staging environment", "Blocked by: pending design approval from Product")
