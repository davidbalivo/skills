---
name: qa-ticket
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger."
---

# Websat QA Ticket

This skill governs how defects found during the manual testing campaign of the critical ERP
workflows (QA Form Inventory sheet) are reported in Jira. It takes the tester's report, runs a short
triage, prevents duplicates, creates the Bug in FREEMA with the canonical fields, and returns the
issue key to paste into the sheet.

## Language

This skill's instructions are written in English, but these rules apply regardless:

- Ticket summary and description are always written in Spanish (Spain).
- Labels stay in English (fixed vocabulary, see Ticket Content).
- Conversation with the user follows the language the user writes in.

## Fixed Jira Coordinates

Use these values on every ticket. Never ask the user for them.

| Field       | Value                                                        |
| ----------- | ------------------------------------------------------------ |
| Cloud ID    | `zvoove.atlassian.net`                                       |
| Project     | `FREEMA`                                                     |
| Issue type  | `Bug`                                                        |
| Parent epic | `FREEMA-4055` (Websat Stab · Workflow QA)                    |
| Application | `customfield_12245` = option `12745` (Websat)                |
| Work Type   | `customfield_13500` = option `14659` (Bug)                   |
| Team        | `customfield_10001` = `31c32486-0452-4550-bce9-6282648334c1` |
| Fix version | `Websat - Stable Relaunch`                                   |

## Tracking Sheet

The tracking sheet is "QA Form Inventory" (Google Drive file id
`1pIfKg0ImwvPHDCxP7-ueFh4FM3d3wYNJaK0Iqll07Dk`). Two tabs feed the skill:

- `Flujos de Test`
  ([`gid=740243351`](https://docs.google.com/spreadsheets/d/1pIfKg0ImwvPHDCxP7-ueFh4FM3d3wYNJaK0Iqll07Dk/edit?gid=740243351#gid=740243351))
  — source of truth for the exact form name (`frmXXX`) and the form criticality (sizing L/M/S).
- `Resultados` — execution tracking, where results and the Jira key are recorded.

The tester's row number in `Resultados` (collected in Intake) anchors the lookup: if Drive access is
available, resolve module, flow, form, environment and test date from that row, then take the exact
form name and criticality from `Flujos de Test`. If Drive access is unavailable, ask the tester
directly and map the module through [module-slugs.md](module-slugs.md).

## Intake

Collect from the tester's free-text report (ask only for what is missing):

- Row number in the `Resultados` tab — anchors the lookup for module, flow, environment and test
  date, then the exact form name and criticality from `Flujos de Test` (see Tracking Sheet).
- Data used (e.g. company/record ids).
- What happened, and what the tester expected.
- Steps to reproduce.
- Screenshots.
- Type (`functional` / `performance` / `technical` / `visual`).
- Priority, as assessed by the tester.

## Ticket Content

Compose labels, summary and description from [template.md](template.md); pick the type variant per
the tester's report.

## Process

1. Parse the tester's report; collect missing intake data.
2. Resolve module, flow and form criticality (sheet if available, otherwise ask).
3. Run the three triage questions.
4. Compose summary, labels, priority and description per the sections above.
5. Show the full preview and wait for explicit confirmation.
6. Create the issue via the Atlassian MCP (`createJiraIssue`) with all Fixed Jira Coordinates;
   attempt the fix version, drop it with a warning if it does not exist.
7. Return the issue key and URL, and remind the tester to paste the key into the "Ticket Jira"
   column of the sheet row.
