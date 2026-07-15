---
name: qa-ticket
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger. Create standardized Jira bugs for Websat workflow QA from tester reports and QA Form Inventory data."
---

# Websat QA Ticket

This skill governs how defects found during the manual testing campaign of the critical ERP
workflows (QA Form Inventory sheet) are reported in Jira. It takes the tester's report, creates
the Bug in FREEMA with the canonical fields, and returns the issue key to paste into the sheet.

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

## Classification

Classify by observed behavior, not by suspected root cause:

- **Functional** · Incorrect outcome, error, exception, blockage, crash or incorrect data.
- **Performance** · Correct outcome, but unacceptable completion time.
- **Visual** · Correct behavior, but incorrect presentation.

Treat error messages, stacktraces and correlation IDs as technical evidence for a functional defect,
not as a separate type. Infer the type from the report; when ambiguous, ask only about the observed
behavior needed to classify it.

## Intake

Collect from the tester's free-text report (ask only for what is missing):

- Row number in the `Resultados` tab — anchors the lookup for module, flow, environment and test
  date, then the exact form name and criticality from `Flujos de Test` (see Tracking Sheet).
- Data used (e.g. company/record ids).
- What happened, and what the tester expected.
- Steps to reproduce.
- Evidence appropriate to the defect (e.g. screenshots, measurements or logs).
- Priority, as assessed by the tester.

## Ticket Content

Compose labels, summary and description from the canonical template and type-specific fill rules in
[template.md](template.md). Keep the same description sections for every defect type.

## Process

1. Parse the tester's report.
2. Classify the defect by observed behavior per Classification.
3. Collect only the missing intake data appropriate to that classification.
4. Resolve module, flow and form criticality (sheet if available, otherwise ask).
5. Compose summary, labels, priority and description per the sections above.
6. Show the full preview and wait for explicit confirmation.
7. Create the issue via the Atlassian MCP (`createJiraIssue`) with all Fixed Jira Coordinates;
   attempt the fix version, drop it with a warning if it does not exist.
8. Return the issue key and URL, and remind the tester to paste the key into the "Ticket Jira"
   column of the sheet row.
