Generate a changelog entry for the last pull request merged into main. Optionally specify a repository here: $ARGUMENTS.

## Workflow

Follow these instructions explicitly:
1. If a specific repository is provided in $ARGUMENTS (format: owner/repo), use `gh pr list --repo owner/repo --base main --state merged --limit 1 --json number,title,author,mergedAt,url` to find the last merged PR
2. Otherwise, use `gh search prs --involves @me --base main --state merged --sort updated --order desc --limit 1 --json number,title,repository,author,mergedAt,url` to find the last PR merged into main across all repositories
3. For the PR found, use `gh pr view <number> --repo owner/repo --json number,title,body,author,mergedAt,mergedBy,url,additions,deletions,changedFiles,commits,labels` to fetch complete PR details
4. Generate a well-formatted changelog entry from the PR information

## Changelog Format

Create a changelog entry with the following structure:

### PR Title (#PR_NUMBER)
**Merged:** [Date in YYYY-MM-DD format]
**Author:** @author_username
**PR URL:** [full PR URL]

#### Description
[PR body/description - if empty, note "No description provided"]

#### Changes
- **Files changed:** X files
- **Lines added:** +X
- **Lines removed:** -X
- **Total commits:** X

#### Commits
[List all commits in the PR with format: "- commit_message (short_sha)"]

#### Labels
[List PR labels if any, otherwise note "No labels"]

## Output

Present the changelog as clean, formatted markdown suitable for copying into a CHANGELOG.md file. If no PR is found, provide a clear message indicating that no merged PRs were found for the specified criteria.

## Dependencies

If this prompt errors on missing commands, provide the user with the link below:
- GitHub CLI (gh): https://cli.github.com/
