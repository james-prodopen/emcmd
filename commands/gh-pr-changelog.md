Generate functional changelog entries for pull requests merged into main by analyzing actual code changes. Arguments: $ARGUMENTS

## Arguments

Arguments can be provided in any order, space-separated:
- **Repository** (optional): Format `owner/repo` - If omitted, searches across all accessible repos
- **Days** (optional): Number of days back to search for PRs - Default is 7, use a number like `3`, `14`, or `30`
- **Author** (optional): Filter by author - Use `@username` format (e.g., `@octocat`)

**Examples:**
- No arguments: Process PRs from last 7 days across all repos, all authors
- `owner/repo`: Process PRs from last 7 days in specified repo, all authors
- `14`: Process PRs from last 14 days across all repos, all authors
- `@username`: Process PRs from last 7 days by specific author across all repos
- `owner/repo 3`: Process PRs from last 3 days in specified repo, all authors
- `owner/repo @username`: Process PRs from last 7 days by specific author in specified repo
- `30 @username`: Process PRs from last 30 days by specific author across all repos
- `owner/repo 14 @username`: Process PRs from last 14 days by specific author in specified repo

## Workflow

Follow these instructions explicitly:
1. Parse $ARGUMENTS to extract repository, days (default: 7), and author filter:
   - Repository: matches pattern `owner/repo` (contains `/`)
   - Days: matches a number (e.g., `3`, `7`, `14`, `30`)
   - Author: starts with `@` (e.g., `@username`)
2. Calculate the date N days ago from today in YYYY-MM-DD format (today's date is available in the environment)
3. Build the appropriate `gh` command based on arguments:
   - If repository is specified: `gh pr list --repo owner/repo --search "base:main merged:>=YYYY-MM-DD [author:username]" --state merged --json number,title,author,mergedAt,url,repository`
   - Otherwise: `gh search prs "base:main is:merged merged:>=YYYY-MM-DD [author:username]" --sort updated --order desc --json number,title,repository,author,mergedAt,url`
4. For each PR found, use `gh pr view <number> --repo owner/repo --json number,title,body,author,mergedAt,mergedBy,url,additions,deletions,changedFiles,files,labels` to fetch complete PR details
5. For each PR, use `gh pr diff <number> --repo owner/repo` to get the full code diff/patch
6. Analyze the code changes to understand what functionality was added, modified, or removed
7. Generate a functional changelog entry for each PR based on the analysis
8. Present all changelog entries in chronological order (most recent first)

## Analysis Instructions

Carefully analyze the PR diff to identify and categorize changes:

**Features** - New functionality, capabilities, or enhancements
- Look for new functions, classes, modules, or API endpoints
- Identify new configuration options or settings
- Note new user-facing features or UI components

**Bug Fixes** - Corrections to existing functionality
- Identify fixes to logic errors, edge cases, or incorrect behavior
- Note corrections to error handling or validation
- Look for fixes mentioned in commit messages or PR description

**Refactoring** - Code improvements without functional changes
- Identify code reorganization, renaming, or restructuring
- Note improvements to code quality, readability, or performance
- Look for extraction of common logic or consolidation

**Documentation** - Changes to docs, comments, or README files
- Identify new or updated documentation files
- Note significant comment additions or improvements

**Dependencies** - Changes to external dependencies
- Identify package.json, requirements.txt, go.mod, or similar changes
- Note version upgrades or new dependencies added

**Testing** - Changes to test files or test infrastructure
- Identify new test cases or test file additions
- Note changes to test configuration or testing utilities

**Configuration** - Changes to config files, build scripts, or CI/CD
- Identify changes to workflow files, build configs, or environment settings

## Changelog Format

Create a functional changelog entry with the following structure:

### [PR Title] (#PR_NUMBER)
**Merged:** [Date in YYYY-MM-DD format]
**Author:** @author_username
**PR URL:** [full PR URL]

#### Summary
[1-2 sentence overview of what this PR accomplishes from a functional perspective]

#### Features
[List new features or enhancements with functional descriptions - what they enable users/developers to do]
- [Feature description based on code analysis]

#### Bug Fixes
[List bugs fixed with descriptions of what was corrected]
- [Bug fix description based on code analysis]

#### Refactoring
[List significant refactoring changes if any]
- [Refactoring description]

#### Documentation
[List documentation changes if any]
- [Documentation updates]

#### Dependencies
[List dependency changes if any]
- [Dependency updates]

#### Testing
[List testing changes if any]
- [Test additions/improvements]

#### Technical Details
- **Files changed:** X files ([list key files modified])
- **Lines added:** +X
- **Lines removed:** -X

## Output Requirements

- Generate a changelog entry for each PR in the result set
- Present entries in chronological order (most recent first)
- Only include sections that have relevant changes (omit empty sections)
- Focus on WHAT changed functionally, not just HOW it changed in code
- Write descriptions that are meaningful to users/developers reading the changelog
- Use the PR description and commit messages as context, but base the changelog on actual code analysis
- Present the changelog as clean, formatted markdown suitable for copying into a CHANGELOG.md file
- If no PRs are found, provide a clear message indicating that no merged PRs were found for the specified criteria
- Add a separator line (`---`) between each PR entry for readability

## Dependencies

If this prompt errors on missing commands, provide the user with the link below:
- GitHub CLI (gh): https://cli.github.com/
