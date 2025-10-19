# emcmd - Engineering Manager Commands

A collection of slash commands to help engineering managers automate common workflows.

## Available Commands

- **`gh-jira-1on1-prep`** - Prepare for 1-on-1 meetings by reviewing a developer's recent Jira work items and GitHub PRs
- **`gh-pr-blockers`** - Identify PRs that appear blocked: both explicitly (e.g. on review) and implicitly (e.g. author is stuck)
- **`jira-comment-blockers`** - Search through current sprint work items for blockers raised in Jira comments
- **`jira-duplicate-bugs`** - Find potential duplicate bugs in a Jira project that aren't already linked
- **`jira-external-dependencies`** - Identify work items in a Jira project that are blocked by external dependencies

## Installation

### For Claude Code

> [!TIP]
> Quick start below, for full instructions refer to the [docs](https://docs.claude.com/en/docs/claude-code/slash-commands#custom-slash-commands) and [best practices](https://www.anthropic.com/engineering/claude-code-best-practices).

To add personal commands (commands available across all your projects):
1. If `~/.claude/commands/` does not yet exist:
    ```bash
    mkdir -p ~/.claude/commands
    ```

2. Copy the command files from this repository's `commands/` folder to your Claude Code commands directory:
   ```bash
   cp commands/*.md ~/.claude/commands/
   ```

3. Restart Claude Code (`claude --continue` to resume where you left off).

#### Optional: Allowlist Commands in Settings

> [!WARNING]
> This is a general allowlist, not specific to these commands.

To avoid repeated approvals, you can allowlist specific CLI commands/MCP tools in your `settings.json`. 

**Example for `/gh-pr-blockers` command:**

For example, to allowlist GitHub CLI commands used by `gh-pr-blockers.md` globally for your user, add this to your `~/.claude/settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Bash(gh pr list *)",
      "Bash(gh search prs *)",
      "Bash(gh pr view *)"
    ]
  }
}
```

> [!TIP]
> Refer to docs [here](https://docs.claude.com/en/docs/claude-code/settings#permission-settings) and [here](https://docs.claude.com/en/docs/claude-code/iam#tool-specific-permission-rules) for additional details.

### For Cursor

> [!TIP]
> Quick start below, for full instructions refer to the [docs](https://cursor.com/docs/agent/chat/commands#creating-commands).

To add global commands:
1. If `~/.cursor/commands/` does not yet exist:
    ```bash
    mkdir -p ~/.cursor/commands/
    ```

2. Copy the command files from this repository's `commands/` folder to your Cursor commands directory:
   ```bash
   cp commands/*.md ~/.cursor/commands/
   ```

3. Restart Cursor.

#### Optional: Allowlist Commands in Settings

> [!WARNING]
> This is a general allowlist, not specific to these commands.

To avoid repeated approvals, you can allowlist specific CLI commands/MCP tools in **Cursor Settings**.

1. Go to **Cursor Settings** > **Chat** > **Auto-Run**.

2. Set **Auto-Run Mode** to **Use Allowlist**.

3. Add commmands or MCP tools to **Command Allowlist** or **MCP Allowlist**, respectively.

For example, to allowlist GitHub CLI commands used by `gh-pr-blockers.md`, add `gh pr list`, `gh search prs` and `gh pr view` to the **Command Allowlist**.

### For Visual Studio Code

VS Code/GitHub Copilot supports custom slash commands as prompt files.

> [!TIP]
> Quick start below, for full instructions refer to the [docs](https://code.visualstudio.com/docs/copilot/customization/prompt-files#_create-a-prompt-file).

1. Enable prompt files in VS Code settings: `chat.promptFiles`

2. Create a new prompt file:
   - Using the **Command Palette**, find **Chat: New Prompt File**
   - Choose whether to create a workspace prompt file (available only within the workspace) or user prompt file (available across workspaces). See docs linked above for details.
   - Enter a prompt file name (e.g., `gh-pr-blockers`)

3. Copy the content from the corresponding command file in this repository's `commands/` folder into your new `.prompt.md` file

4. Use the prompt by typing `/` followed by the filename in Copilot Chat (e.g., `/gh-pr-blockers`)

## Contributing

Feel free to submit an issue/open a PR for revisions/new commands that would be helpful for engineering managers!
