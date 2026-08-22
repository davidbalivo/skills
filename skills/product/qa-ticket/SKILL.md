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
- Labels stay in English (fixed vocabulary in [template.md](template.md)).
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

Unlike the values above, priority, effort and area come from the tester (Intake). Priority accepts
exactly these values:

| Priority | Notes        |
| -------- | ------------ |
| `Major`  |              |
| `High`   |              |
| `Medium` | Jira default |
| `Low`    |              |
| `Lowest` |              |

Effort (`customfield_15445`) and Area (`customfield_15446`) are select fields set by option value:

- Effort · `S`, `M`, `L`. Never deduced; without a tester value the field stays unset.
- Area · `APIs`, `Applications`, `Architecture`, `Finance`, `Infrastructure`, `Labor`,
  `Operations`, `UI/UX`. Without a tester value, deduce the closest area from the defect and the
  row context; cross-cutting areas (`UI/UX`, `Architecture`) win over the module's domain, and
  `Applications` covers software outside the ERP (e.g. mobile app). A deduced area is flagged in
  the preview (Step 7) for the tester to confirm.

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
| T      | `Ticket Jira`     |

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

The tester provides the visible Google Sheets row number in the `Resultados` tab (Intake). It is a
literal, 1-based row number used directly in A1 notation: row `12` means `Resultados!A12:U12`, and
its Jira tracking cell is `Resultados!T12`. Never add or subtract `1`, convert it to a zero-based
index, or use `ID prueba` as the lookup key.

That exact row resolves `ID prueba`, module, flow, environment and execution date. It must be an
execution row, not a module heading or an empty row.

### Form Resolution

The flow row in `Flujos de Test` is located by the exact `Flujo` value read from `Resultados`.
Without a match, the ticket goes without form and criticality, keeping a form only if the report
names one.

Read `Forms en flujo` as `N` and treat that row plus the following `N-1` rows as the candidate
forms; continuation rows may leave `Flujo` blank. A single candidate is selected automatically.
Among several, an exact form named in the report wins if it matches a candidate; otherwise the
tester designates the affected form (Step 3). Take `Formulario` and `Sizing` from the selected
row.

### Without Drive Access

Without sheet access the whole context comes from the tester: `ID prueba`, module, flow,
environment, execution date, exact affected form and form criticality. The module maps through
[module-slugs.md](module-slugs.md), and the row number keeps anchoring the final tracking
reminder.

## Classification

Classify by observed behavior, not by suspected root cause:

- **Functional** · Incorrect outcome, error, exception, blockage, crash or incorrect data.
- **Performance** · Correct outcome, but unacceptable completion time.
- **Visual** · Correct behavior, but incorrect presentation.

Treat error messages, stacktraces and correlation IDs as technical evidence for a functional defect,
not as a separate type.

## Intake

**Mandatory fields**

- Google Sheets row number in the `Resultados` tab.
- Current behavior observed.
- Steps to reproduce.

**Optional fields**

- Expected behavior.
- Data used (e.g. company/record ids).
- Navigation route to the affected screen (`Menú → Submenú → Pantalla`), part of the steps to
  reproduce.
- Priority, as assessed by the tester; if none is given, use Medium instead of inferring one.
- Effort, from its accepted values in Fixed Jira Coordinates; unset when not given.
- Area, from its accepted values in Fixed Jira Coordinates; deduced when not given.

Include any extra information the tester volunteers (evidence, impact, workaround) in its template
section. Never invent it and never write an empty placeholder.

## Ticket Content

Compose labels, summary and description from the canonical template in [template.md](template.md).
The core description sections do not vary by defect type.

Any non-mandatory value that is empty or cannot be extracted, labels included, stays out of the
ticket; the omissions are reported in the preview (Step 7).

## Similar Ticket Detection

Immediately before creation, compare the confirmed ticket with every existing issue whose parent
is `FREEMA-4055`. Retrieve all pages with the Atlassian MCP (`searchJiraIssuesUsingJql`) using
`parent = FREEMA-4055 ORDER BY created DESC`, including at least the key, summary, description,
status and labels. Do not exclude resolved or closed issues.

Judge similarity from the defect itself: the observed behavior, reproduction steps and affected
form or flow. Classification and labels are supporting signals. Sharing only a form, flow, label or
suspected root cause does not make two tickets similar. Treat different wording as a match when it
describes the same defect.

Return only credible exact or similar matches to the tester. For each match, include its key,
summary, status, Jira link and a brief concrete reason for the match. Never create the issue until
the tester explicitly chooses whether to continue or cancel. If the Jira search cannot be
completed, report the failure and wait to retry; never bypass this check.

## Steps

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
on an optional field. Suggest fields with a fixed value set (priority, effort, area) with their
accepted values in parentheses, e.g. `Effort (S, M, L)`.

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
- If the row already has a `Ticket Jira` value, warn the tester that the row points to an existing
  ticket and continue.
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

### 5. Ticket Content

Compose summary, labels, priority and description per Ticket Content, [template.md](template.md)
and the tickets in [examples.md](examples.md). When the tester gave no expected behavior and the
report makes it obvious, infer `Comportamiento esperado` from context. When the tester gave no
area, deduce it per Fixed Jira Coordinates. This is internal work; show nothing to the tester yet.

Write tersely; cut every word that adds no information.

Go to Step 6.

### 6. Adversarial Review

Switch from author to attacker. Check the composed ticket against every rule in this skill,
[template.md](template.md) and [examples.md](examples.md). On any finding, fix the ticket and
review again until none remains.

Verbose passages count as findings.

Go to Step 7.

### 7. Preview 🔁

Show the tester the full ticket in chat (summary, labels, priority, description, plus effort and
area when set) and wait for explicit confirmation. Never create the issue without it. Flag an
inferred `Comportamiento esperado` and a deduced area as inferred so the tester confirms or
corrects them, and list any field omitted because its value was missing or could not be extracted.

Trim any verbosity that survived Step 6 before showing the ticket.

If the ticket carries no `Evidencia` section, offer in the same message:

```md
¿Quieres que deje preparada la sección Evidencia para que adjuntes capturas manualmente? (yo no
puedo subir imágenes a Jira)
```

If accepted, add the section per Evidence Handling in [template.md](template.md).

When the ticket already carries the pending upload placeholder, warn in the same message that the
images must be uploaded manually in Jira.

- If the tester requests changes, apply them and go back to Step 5.
- If the tester confirms, go to Step 8.

### 8. Similar Ticket Check 🔁

Apply Similar Ticket Detection to the final ticket confirmed in Step 7.

If there are no credible matches, go to Step 9 without asking for another confirmation.

If one or more matches exist, show all of them and wait for the tester's explicit decision:

```md
⚠️ He encontrado tickets que podrían ser iguales o similares:

- [{FREEMA-XXXX}]({url}) · {summary} · {status}
  - Coincidencia · {brief concrete reason}

¿Quieres crear el ticket igualmente o cancelar la creación?
```

- If the tester chooses to continue, go to Step 9.
- If the tester cancels, report `Creación cancelada. No se ha creado ningún ticket.` and stop.
- If the tester requests ticket changes, apply them and go back to Step 5; repeat the preview and
  similarity check after the changes.
- If the decision is ambiguous, ask again. Never infer permission to create.

### 9. Creation

Create the issue via the Atlassian MCP (`createJiraIssue`) with all Fixed Jira Coordinates.
Include effort only when the tester gave it, and area whenever set.

Then report:

```md
✅ Ticket creado · [{FREEMA-XXXX}]({url})

- Pega la clave en la celda `T{fila}` de Resultados.
- Sube las capturas al ticket manualmente.
```

Include the captures line only when the tester accepted the Evidencia offer in Step 7.
