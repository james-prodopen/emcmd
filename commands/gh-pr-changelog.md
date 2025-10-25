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
2. Build the appropriate `gh` command based on arguments:
   - If repository is specified: `gh pr list --repo owner/repo --search "base:main merged:>={today - N days} [author:username]" --state merged --json number,title,author,mergedAt,url,repository`
   - Otherwise: `gh search prs "base:main is:merged merged:>={today - N days} [author:username]" --sort updated --order desc --json number,title,repository,author,mergedAt,url`
   - Replace `N` with the number of days from step 1 (e.g., `7`, `14`, `30`)
   - Replace `[author:username]` with the author filter if provided, otherwise omit it
3. For each PR found, use `gh pr view <number> --repo owner/repo --json number,title,body,author,mergedAt,mergedBy,url,additions,deletions,changedFiles,files,labels` to fetch complete PR details
4. For each PR, use `gh pr diff <number> --repo owner/repo` to get the full code diff/patch
5. **IMPORTANT - Read full files for context:**
   - Clone or fetch the repository if not already available locally
   - For EACH file that was changed in the PR (identified from the files list in step 3), read the ENTIRE file in its current state (after the PR was merged)
   - Do NOT rely solely on the diff - read the complete files to understand the full context of changes
   - If the changes reference or interact with other files (imports, function calls, etc.), read those related files as well for additional context
   - Use the Read tool to read these files - this gives you the complete picture of what the code does
6. Analyze the code changes to understand what functionality was added, modified, or removed:
   - Use the diff to identify what changed
   - Use the full file contents to understand the context and purpose of those changes
   - Use related files to understand how the changes integrate with the rest of the codebase
7. Generate a functional changelog entry for each PR based on the comprehensive analysis
8. Present all changelog entries in chronological order (most recent first)

## Analysis Instructions

**CRITICAL: Read full files, not just diffs!** You MUST read the complete contents of changed files and related files to generate accurate, functional changelog entries. The diff alone does not provide enough context to understand what the code actually does.

For each PR, after reading the full files as instructed in step 5, carefully analyze to identify and categorize changes:

**Features** - New functionality, capabilities, or enhancements
- Look for new functions, classes, modules, or API endpoints (read the full implementation to understand what they do)
- Identify new configuration options or settings (read related config files to understand their impact)
- Note new user-facing features or UI components (read the full component files to understand their purpose)
- Describe WHAT the feature enables users/developers to do, not just what code was added

**Bug Fixes** - Corrections to existing functionality
- Identify fixes to logic errors, edge cases, or incorrect behavior (read surrounding code to understand the fix context)
- Note corrections to error handling or validation (read the full error handling flow)
- Look for fixes mentioned in commit messages or PR description
- Describe WHAT was broken and HOW it's now fixed from a user perspective

**Refactoring** - Code improvements without functional changes
- Identify code reorganization, renaming, or restructuring (read both old and new file structures)
- Note improvements to code quality, readability, or performance
- Look for extraction of common logic or consolidation (read the files where logic was extracted from/to)

**Documentation** - Changes to docs, comments, or README files
- Identify new or updated documentation files (read the full docs to understand what was documented)
- Note significant comment additions or improvements

**Dependencies** - Changes to external dependencies
- Identify package.json, requirements.txt, go.mod, or similar changes
- Note version upgrades or new dependencies added
- Read the dependency files to understand the full scope of changes

**Testing** - Changes to test files or test infrastructure
- Identify new test cases or test file additions (read full test files to understand what's being tested)
- Note changes to test configuration or testing utilities

**Configuration** - Changes to config files, build scripts, or CI/CD
- Identify changes to workflow files, build configs, or environment settings
- Read full config files to understand the complete configuration context

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

- **You MUST read the full files** as specified in the Workflow section - this is not optional
- Generate a changelog entry for each PR in the result set based on deep code analysis
- Present entries in chronological order (most recent first)
- Only include sections that have relevant changes (omit empty sections)
- Focus on WHAT changed functionally, not just HOW it changed in code
- Write descriptions that are meaningful to users/developers reading the changelog, based on your understanding from reading the full files
- Use the PR description and commit messages as context, but base the changelog on actual code analysis from reading complete files
- Your changelog entries should demonstrate deep understanding of the code - this is only possible by reading full files
- Present the changelog as clean, formatted markdown suitable for copying into a CHANGELOG.md file
- If no PRs are found, provide a clear message indicating that no merged PRs were found for the specified criteria
- Add a separator line (`---`) between each PR entry for readability

## Dependencies

If this prompt errors on missing commands, provide the user with the link below:
- GitHub CLI (gh): https://cli.github.com/
