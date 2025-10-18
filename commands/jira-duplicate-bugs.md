Find potential duplicate work items in a Jira project. Provide PROJECT_KEY here: $ARGUMENTS.

## Workflow

Follow these instructions explicitly:
1. Use `acli jira auth status` to get the "Site" value, which will be used to generate URLs later
2. Use `acli jira workitem search --json --jql "project={PROJECT_KEY} and issuetype=Bug" --paginate` to fetch all work items in the project, iterating through all pages if need be
3. Output a working table of unique work item pairs, where at least one work item in the pair does not have status.name of "Done" - for example, assuming you have work items A and B
    - if (A status.name != "Done" AND B status.name != "Done") OR (A status.name = "Done" AND B status.name != "Done") OR (A status.name != "Done" AND B status.name = "Done") include a row (A,B) in the table
    - do not create a duplicative row (B,A) in addition to (A,B)
    - table schema should be (A.key | A.summary | B.key | B.summary)
    - DO NOT truncate summary text
4. Did you output a working table in step 3? If not, go back to step 3 and output a working table - DO NOT proceed unless that has been completed
5. Analyze all work item PAIRS in the previously created working table to identify potential duplicates based on similar summary text
    - when analyzing, consider only a single row at a time to avoid finding work item pairs that both have status.name = "Done"
6. For each potential duplicate pair, use `acli jira workitem link list --key "{key}" --json` on BOTH of the work items to check if they are already linked to each other
    - you need to check both work items because that acli command supports "duplicates" but not "is duplicated by" relationships
7. If the pair of work items is not already linked, use `acli jira workitem view "{key}" --fields "issuetype,summary,description"` for both work items to review them in additional detail and increase confidence that they are duplicates
8. For each pair of work items that are duplicates, generate URLs for each work item using the format: `https://{site}/browse/{key}`

## Duplicate Detection

Consider work items as potential duplicates if they have:
- **Identical or highly similar summaries** - Same or nearly identical title text
- **Similar descriptions** - Overlapping problem statements or requirements

## Output

Create a table of potential duplicates that are **not already linked**. For each unique pair:

| Work Item 1 | Work Item 2 |
|-------------|-------------|
| [KEY-123](https://{site}/browse/KEY-123) | [KEY-456](https://{site}/browse/KEY-456) |
| [KEY-789](https://{site}/browse/KEY-789) | [KEY-101](https://{site}/browse/KEY-101) |

If no unlinked duplicates are found, report: "No potential duplicates found that aren't already linked." If A and B are linked, don't include rows for (A,B) and (B,A) - just include a single row for (A,B).

## Dependencies

If this prompt errors on missing commands, provide the user with the link below:
- Atlassian CLI (acli): https://developer.atlassian.com/cloud/acli/guides/install-acli/
