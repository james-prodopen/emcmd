Generate a functional changelog entry for the last pull request merged into main by analyzing the actual code changes. Optionally specify a repository here: $ARGUMENTS.

## Workflow

Follow these instructions explicitly:
1. If a specific repository is provided in $ARGUMENTS (format: owner/repo), use `gh pr list --repo owner/repo --base main --state merged --limit 1 --json number,title,author,mergedAt,url,repository` to find the last merged PR
2. Otherwise, use `gh search prs --involves @me --base main --state merged --sort updated --order desc --limit 1 --json number,title,repository,author,mergedAt,url` to find the last PR merged into main across all repositories
3. For the PR found, use `gh pr view <number> --repo owner/repo --json number,title,body,author,mergedAt,mergedBy,url,additions,deletions,changedFiles,files,labels` to fetch complete PR details including the list of changed files
4. Use `gh pr diff <number> --repo owner/repo` to get the full code diff/patch for the PR
5. Analyze the code changes to understand what functionality was added, modified, or removed
6. Generate a functional changelog entry based on the analysis

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

- Only include sections that have relevant changes (omit empty sections)
- Focus on WHAT changed functionally, not just HOW it changed in code
- Write descriptions that are meaningful to users/developers reading the changelog
- Use the PR description and commit messages as context, but base the changelog on actual code analysis
- Present the changelog as clean, formatted markdown suitable for copying into a CHANGELOG.md file
- If no PR is found, provide a clear message indicating that no merged PRs were found for the specified criteria

## Dependencies

If this prompt errors on missing commands, provide the user with the link below:
- GitHub CLI (gh): https://cli.github.com/
