Generate functional changelog entries for pull requests merged into main by analyzing actual code changes. Arguments: $ARGUMENTS

## Arguments

Arguments can be provided in any order, space-separated:
- **Repository** (optional): Format `owner/repo` - If omitted, searches across all accessible repos
- **Count** (optional): Number of PRs to process - Default is 10, use a number like `5` or `20`
- **Author** (optional): Filter by author - Use `@username` format (e.g., `@octocat`)

**Examples:**
- No arguments: Process 10 most recent PRs across all repos, all authors
- `owner/repo`: Process 10 most recent PRs in specified repo, all authors
- `20`: Process 20 most recent PRs across all repos, all authors
- `@username`: Process 10 most recent PRs by specific author across all repos
- `owner/repo 5`: Process 5 most recent PRs in specified repo, all authors
- `owner/repo @username`: Process 10 most recent PRs by specific author in specified repo
- `20 @username`: Process 20 most recent PRs by specific author across all repos
- `owner/repo 5 @username`: Process 5 most recent PRs by specific author in specified repo

## Workflow

Follow these instructions explicitly:
1. Parse $ARGUMENTS to extract repository, count (default: 10), and author filter:
   - Repository: matches pattern `owner/repo` (contains `/`)
   - Count: matches a number (e.g., `5`, `10`, `20`)
   - Author: starts with `@` (e.g., `@username`)
2. Build the appropriate `gh` command based on arguments:
   - If repository is specified: `gh pr list --repo owner/repo --base main --state merged --limit <count> [--author <username>] --json number,title,author,mergedAt,url,repository`
   - Otherwise: `gh search prs --base main --state merged --sort updated --order desc --limit <count> [--author <username>] --json number,title,repository,author,mergedAt,url`
3. For each PR found, use `gh pr view <number> --repo owner/repo --json number,title,body,author,mergedAt,mergedBy,url,additions,deletions,changedFiles,files,labels` to fetch complete PR details
4. For each PR, use `gh pr diff <number> --repo owner/repo` to get the full code diff/patch
5. Analyze the code changes to understand what functionality was added, modified, or removed
6. Generate a functional changelog entry for each PR based on the analysis
7. Present all changelog entries in chronological order (most recent first)

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
