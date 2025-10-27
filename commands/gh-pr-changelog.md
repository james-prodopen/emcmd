Generate changelog for recent PRs merged into main (args: owner/repo days author). Arguments: $ARGUMENTS

## Arguments

Arguments must be provided in order, space-separated:
1. **Repository** (required): Format `owner/repo`
2. **Days** (optional): Number of days back to search for PRs - Default is 7, use a number like `3`, `14`, or `30`
3. **Author** (optional): Filter by author - GitHub username

**Examples:**
- `owner/repo`: Process PRs from last 7 days in specified repo, all authors
- `owner/repo 3`: Process PRs from last 3 days in specified repo, all authors
- `owner/repo 7 username`: Process PRs from last 7 days by specific author in specified repo
- `owner/repo 14 username`: Process PRs from last 14 days by specific author in specified repo

## Workflow

Follow these instructions explicitly:
1. Parse $ARGUMENTS in order to extract repository (required), days (default: 7), and author filter:
   - First argument: Repository in format `owner/repo` - REQUIRED, if not provided, ask the user for it
   - Second argument (optional): Days as a number (e.g., `3`, `7`, `14`, `30`) - defaults to 7 if not provided
   - Third argument (optional): Author as a GitHub username
2. Build the `gh` command:
   - `gh pr list --repo owner/repo --search "base:main merged:>={today's date - N days} [author:username]" --state merged --json number,title,author,mergedAt,url`
   - Replace `N` with the number of days from step 1 (e.g., `7`, `14`, `30`)
   - Replace `[author:username]` with the author filter if provided, otherwise omit it
3. For each PR found, use `gh pr view <number> --repo owner/repo --json number,title,body,author,mergedAt,mergedBy,url,additions,deletions,changedFiles,files,labels,comments,reviews` to fetch complete PR details including all comments and reviews
4. For each PR, use `gh pr diff <number> --repo owner/repo` to get the full code diff/patch
5. Analyze the PR to understand what functionality was added, modified, or removed:
   - Review the diff to identify what changed in the code
   - Read the PR title, body/description, and all comments for context about the purpose and intent
   - Review any discussion in comments and reviews for additional context about decisions made
   - Use the labels to help categorize the type of change (bug, enhancement, etc.)
6. Generate a functional changelog entry for each PR based on the comprehensive analysis
7. Present all changelog entries in chronological order (most recent first)

## Analysis Instructions

**Features** - New functionality, capabilities, or enhancements
- Look for new functions, classes, modules, or API endpoints in the diff
- Identify new configuration options or settings
- Note new user-facing features or UI components
- Use the PR description and comments to understand the purpose
- Describe WHAT the feature enables users/developers to do, not just what code was added

**Bug Fixes** - Corrections to existing functionality
- Identify fixes to logic errors, edge cases, or incorrect behavior in the diff
- Note corrections to error handling or validation
- Look for fixes mentioned in the PR title, description, or comments
- Describe WHAT was broken and HOW it's now fixed from a user perspective

**Refactoring** - Code improvements without functional changes
- Identify code reorganization, renaming, or restructuring in the diff
- Note improvements to code quality, readability, or performance
- Look for extraction of common logic or consolidation

**Documentation** - Changes to docs, comments, or README files
- Identify new or updated documentation files in the changed files list
- Note significant comment additions or improvements

**Dependencies** - Changes to external dependencies
- Identify package.json, requirements.txt, go.mod, or similar changes in the diff
- Note version upgrades or new dependencies added

**Testing** - Changes to test files or test infrastructure
- Identify new test cases or test file additions
- Note changes to test configuration or testing utilities

**Configuration** - Changes to config files, build scripts, or CI/CD
- Identify changes to workflow files, build configs, or environment settings in the diff

## Changelog Format

Create a concise changelog entry with the following structure - and absolutely nothing else:

### [PR Title] (#PR_NUMBER)
**Merged:** [Date in YYYY-MM-DD format] | **Author:** @author_username | [PR URL]

**Changes:**
- [Bullet points describing the change]

**Technical Details:** X files changed (+X/-X lines)

## Output Requirements

- Generate a concise changelog entry for each PR in the result set based on analysis of the diff, PR description, comments, and reviews
- Present entries in chronological order (most recent first)
- Focus on WHAT changed functionally, not just HOW it changed in code
- Keep the "Changes:" section brief - typically 1-3 bullet points covering the core changes
- Write descriptions that are meaningful to users/developers reading the changelog
- Use the PR title, description, comments, and reviews to understand the context and purpose of changes
- Present the changelog as clean, formatted markdown suitable for copying into a CHANGELOG.md file
- If no PRs are found, provide a clear message indicating that no merged PRs were found for the specified criteria
- Add a separator line (`---`) between each PR entry for readability

## Best Practices

- Never add `--limit` flag to a CLI command on your own, as this could limit the PRs the user sees in the changelog and be very confusing
   - If there appear to be any issues with the volume of data retured by CLI calls, notify the user and ask whether it is acceptable to attempt to add the `--limit` flag to the specific command
   - Only consider adding a `--limit` flag to the `gh pr list` and `gh search prs` commands
- The Changelog is the only output

## Dependencies

If this prompt errors on missing CLI ommands, provide the user with the link below:
- GitHub CLI (gh): https://cli.github.com/
