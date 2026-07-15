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

## QA Data Sources

The source workbook is "QA Form Inventory" (Google Drive file id
`1pIfKg0ImwvPHDCxP7-ueFh4FM3d3wYNJaK0Iqll07Dk`). Two tabs feed the skill:

- `Resultados` — execution context and tracking, including module, flow, environment, execution
  date, results and Jira key.
- `Flujos de Test`
  ([`gid=740243351`](https://docs.google.com/spreadsheets/d/1pIfKg0ImwvPHDCxP7-ueFh4FM3d3wYNJaK0Iqll07Dk/edit?gid=740243351#gid=740243351))
  — source of truth for the exact form name (`frmXXX`) and the form criticality (sizing L/M/S).

## Test Context Resolution

### Execution Lookup

Ask the tester for the visible Google Sheets row number in the `Resultados` tab. Treat it as a
literal, 1-based row number and use it directly in A1 notation: row `12` means
`Resultados!A12:U12`, and its Jira tracking cell is `Resultados!T12`. Never add or subtract `1`,
convert it to a zero-based index, or use `ID prueba` as the lookup key.

If Drive access is available, read that exact `Resultados` row and resolve `ID prueba`, module, flow,
environment and execution date from it. Confirm that the selected row is an execution row rather
than a module heading or an empty row.

### Form Resolution

With Drive access, find the exact flow row in `Flujos de Test`. Read `Forms en flujo` as `N` and treat
that row plus the following `N-1` rows as the candidate forms; continuation rows may leave `Flujo`
blank. Select the form automatically when there is only one candidate. When there are several, use
an exact form named in the report if it matches a candidate; otherwise ask the tester which form is
affected. Take the canonical form name and criticality from the selected `Flujos de Test` row.

### Without Drive Access

Keep the tester-provided visible `Resultados` row number for the final tracking reminder. Ask for
module, flow, environment, execution date, exact affected form and form criticality, then map the
module through [module-slugs.md](module-slugs.md).

## Classification

Classify by observed behavior, not by suspected root cause:

- **Functional** · Incorrect outcome, error, exception, blockage, crash or incorrect data.
- **Performance** · Correct outcome, but unacceptable completion time.
- **Visual** · Correct behavior, but incorrect presentation.

Treat error messages, stacktraces and correlation IDs as technical evidence for a functional defect,
not as a separate type. Infer the type from the report; when ambiguous, ask only about the observed
behavior needed to classify it.

## Intake

First extract everything already available from the tester's report and the selected `Resultados`
row. Then ask only for missing information that is necessary to make the specific defect clear and
reproducible:

- Visible Google Sheets row number in the `Resultados` tab, used literally per Test Context
  Resolution.
- Data used (e.g. company/record ids).
- What the tester expected and what actually happened.
- Navigation route, when reaching the affected screen requires one.
- Steps to reproduce.
- Evidence appropriate to the defect (e.g. screenshots, measurements or logs).
- Priority, as assessed by the tester.

Impact and workaround are optional. If the report already provides them, include them. Otherwise,
offer the tester the option to add them in the conversation language. If the tester declines, omit
the `Impacto` section completely; never invent an impact or write an empty placeholder.

For a performance defect, reuse whatever test context and measurements the tester provides. Ask
only for the additional facts needed to understand and reproduce the reported delay. Do not require
data volume, repetitions or a Winsat measurement by default.

For a visual defect, capture the described difference and available evidence. Images cannot be
attached to Jira automatically through this skill; when an image has no stable URL, tell the tester
that they must upload it to the created issue themselves.

## Ticket Content

Compose labels, summary and description from the canonical template and type-specific fill rules in
[template.md](template.md). Keep the same core description sections for every defect type. Include
the optional `Impacto` section only when the tester provides it.

## Process

1. Parse the tester's report.
2. Classify the defect by observed behavior per Classification.
3. Collect only the missing intake data appropriate to that classification.
4. Resolve module, flow, environment, execution date, form and criticality per Test Context
   Resolution.
5. Compose summary, labels, priority and description per the sections above.
6. Show the full preview and wait for explicit confirmation.
7. Create the issue via the Atlassian MCP (`createJiraIssue`) with all Fixed Jira Coordinates;
   attempt the fix version, drop it with a warning if it does not exist.
8. Return the issue key and URL, and remind the tester to paste the key into the "Ticket Jira"
   column of the row matching `ID prueba`.
