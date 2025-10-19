Find external dependencies for work items in a Jira project. Provide PROJECT_KEY here: $ARGUMENTS.

## Workflow

Follow these instructions explicitly. Under absolutely no circumstances are you to use acli. If the required MCP tools are not available, simply share the content in the Dependencies section below with the user and do absolutely nothing else.
1. Use `mcp__atlassian__getAccessibleAtlassianResources` to get the cloudId (returned simply as "id") and url (which will be the {site_url} used later to generate links to work items)
2. Use `mcp__atlassian__searchJiraIssuesUsingJql` with cloudId from step 1, jql="project={PROJECT_KEY}", and fields=["summary","issuelinks"]
  - do not specify a maxResults - iterate through all pages using nextPageToken
3. For each work item (let's call this work item x) returned, examine the x.fields.issuelinks array
4. For each link in issuelinks (let's call them y), check if:
   - type.name = "Blocks"
   - The link has an "inwardIssue" field but NOT an "outwardIssue" field
   - The inwardIssue.key has a different project prefix than {PROJECT_KEY}
5. If all conditions are met, x has an external dependency on y
6. For each external dependency found, use `mcp__atlassian__getJiraIssue` with the cloudId and issueIdOrKey=inwardIssue.key to get full details
  - do not pass a value for "fields" or "expand"
7. Summarize all external dependencies with their current state

## External Dependency Detection

An external dependency is identified when:
- A work item in {PROJECT_KEY} has a "Blocks" link with an inwardIssue (meaning it is blocked by that issue)
- The inwardIssue.key starts with a different project prefix than {PROJECT_KEY}
- Example: If PROJECT_KEY is "ABC", then ABC-123 blocked by ABC-456 is internal, but ABC-123 blocked by DEF-456 is external

## Output

For each work item (x) that has external dependencies (y), provide:

**External Dependencies**

**"{site_url}/{x_key}" - {x_summary}:**
(for each dependency, y):
\n
→ "is blocked by {site_url}/{x_key}"
  Brief natural language summary of the response from `mcp__atlassian__getJiraIssue` tool call
    - be sure to note status, created and last updated
    - never include times in dates

If no external dependencies are found, report: "No external dependencies found for work items in {PROJECT_KEY}."

## Dependencies

If this prompt errors on missing MCP tools, inform the user as follows:
- If you have not yet configured the Atlassian Rovo MCP, go to https://support.atlassian.com/atlassian-rovo-mcp-server/docs/getting-started-with-the-atlassian-remote-mcp-server/
- If the Atlassian Rovo MCP is configured, retry the command or reconnect and then retry
