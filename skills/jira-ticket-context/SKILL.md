---
name: jira-ticket-context
description: Load and summarize Jira ticket context from the current git branch using acli.
compatibility: opencode
metadata:
  source: acli
  branch_pattern: '[A-Z]+-[0-9]+'
---

## What I do

- Determine the current Jira issue key by inspecting the current git branch name and extracting the first match of `[A-Z]+-[0-9]+` (e.g., `PRO-1234`).
- If no key is found, ask the user for the issue key before proceeding.
- Fetch ticket data using Atlassian CLI: `acli jira workitem view <KEY> --json --fields summary,description,status,assignee,priority,labels`.
- Summarize the ticket in a compact, actionable form.

## Output format

| Field    | Value               |
| -------- | ------------------- |
| Ticket   | `<KEY> — <summary>` |
| Status   | `<status>`          |
| Assignee | `<assignee>`        |
| Priority | `<priority>`        |

Description:

<3-6 bullet points summarizing the description>

Open questions:

<if any>

## Notes

- Prefer the key from the current branch; if it does not exist or is ambiguous, ask for the key.
- If acli is not authenticated, instruct the user to run `acli jira auth login`.
