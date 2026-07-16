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
- Messages to the tester are always written in Spanish (Spain).

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
`1pIfKg0ImwvPHDCxP7-ueFh4FM3d3wYNJaK0Iqll07Dk`). Two tabs feed the skill. In both, headers live in
row `3` and module heading rows fill only column `A`.

`Resultados` · execution context and tracking. Columns used:

| Column | Header            |
| ------ | ----------------- |
| A      | `ID prueba`       |
| B      | `Módulo`          |
| C      | `Flujo`           |
| J      | `Entorno`         |
| K      | `Fecha ejecución` |

`Flujos de Test`
([`gid=740243351`](https://docs.google.com/spreadsheets/d/1pIfKg0ImwvPHDCxP7-ueFh4FM3d3wYNJaK0Iqll07Dk/edit?gid=740243351#gid=740243351))
· source of truth for the exact form name (`frmXXX`) and its criticality. Columns used:

| Column | Header           |
| ------ | ---------------- |
| C      | `Flujo`          |
| E      | `Formulario`     |
| F      | `Sizing` (L/M/S) |
| G      | `Forms en flujo` |

`Flujo` (column `C` in both tabs) is the link between them. The value read from the `Resultados`
row locates the single matching flow row in `Flujos de Test`, by exact value.

## Test Context Resolution

### Execution Lookup

<!-- TODO: una vez tengamos los steps, eliminar lo de pedir al usuario y cosas similares. -->

Ask the tester for the visible Google Sheets row number in the `Resultados` tab. Treat it as a
literal, 1-based row number and use it directly in A1 notation: row `12` means
`Resultados!A12:U12`, and its Jira tracking cell is `Resultados!T12`. Never add or subtract `1`,
convert it to a zero-based index, or use `ID prueba` as the lookup key.

If Drive access is available, read that exact `Resultados` row and resolve `ID prueba`, module, flow,
environment and execution date from it. Confirm that the selected row is an execution row rather
than a module heading or an empty row.

### Form Resolution

With Drive access, locate the flow row in `Flujos de Test` by the exact `Flujo` value read from
`Resultados`. If no row matches, warn the tester and continue without form and criticality,
keeping a form only if the report names one. Read `Forms en flujo` as `N` and treat that row plus
the following `N-1` rows as
the candidate forms; continuation rows may leave `Flujo` blank. Select the form automatically when
there is only one candidate. When there are several, use an exact form named in the report if it
matches a candidate; otherwise ask the tester which form is affected. Take `Formulario` and
`Sizing` from the selected row.

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
row.

**Mandatory fields**

- Google Sheets row number in the `Resultados` tab.
- Current behavior observed.
- Steps to reproduce.

**Optional fields** (suggest them once when missing, never block on them)

- Expected behavior.
- Data used (e.g. company/record ids).
- Priority, as assessed by the tester; if none is given, omit it from the payload so the Jira
  default applies, and never infer one.

Include any extra information the tester volunteers (navigation route, evidence, impact,
workaround) in its template section. Never invent it and never write an empty placeholder.

## Ticket Content

Compose labels, summary and description from the canonical template in [template.md](template.md).
Keep the same core description sections for every defect type and append the optional sections only
when the tester provides them.

## Steps

<!-- TODO: add criticidad de tarea (si no la ha dado el tester, poner medio) -->

### 1. Intake

Extract every Intake field already present in the tester's message. If no field is missing, go to
Step 2 without asking.

Otherwise request everything missing in one single message, omitting the group that is already
complete:

```md
**Para crear el ticket necesito**

- {campos obligatorios que faltan}

**Te recomiendo añadir estos campos también, pero no son obligatorios**

- {campos opcionales que faltan}
```

Repeat until every mandatory field is present. Suggest each optional field only once; never block
on an optional field.

Go to Step 2.

### 2. Drive Access

Try to read the QA Form Inventory workbook once. If it works, go to Step 3.

If it fails, ask the tester to grant access:

```md
⚠️ No puedo leer QA Form Inventory. Necesito acceso a Google Drive. Actívalo y avísame para
reintentar.
```

Retry once after the reply. If it still fails, warn the tester:

```md
⚠️ Sigo sin acceso a la hoja. Te pediré los datos del excel a continuación.
```

Go to Step 3.

### 3. Test Context

With Drive access, inform the tester and resolve the context from the workbook:

```md
🔍 Leyendo QA Form Inventory
```

Resolve module, flow, environment, execution date and `ID prueba` from the `Resultados` row, and
`Formulario` and `Sizing` from `Flujos de Test`, per Test Context Resolution.

- If the row is a module heading or an empty row, report it and ask for the correct row number.
- If the `Flujo` value has no row in `Flujos de Test`, warn the tester and continue:

  ```md
  ⚠️ Este flujo no está en la hoja Flujos de Test. Creo el ticket sin formulario ni criticidad.
  ```

- If several candidate forms exist and none matches the report, ask the tester which form is
  affected.

Without Drive access, ask the tester for the whole context per Without Drive Access.

Go to Step 4.

### 4. Classification

Classify the defect as functional, performance or visual per Classification. If the type stays
ambiguous after reading the report, ask the tester only about the observed behavior needed to
classify it.

Go to Step 5.

## Process

<!-- TODO: eliminar al finalizar -->

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
