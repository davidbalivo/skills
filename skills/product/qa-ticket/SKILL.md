---
name: qa-ticket
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger."
---

# Websat QA Ticket

This skill governs how defects found during the manual testing campaign of the critical ERP
workflows (QA Form Inventory sheet) are reported in Jira. It takes the tester's report, runs a short
triage, prevents duplicates, creates the Bug in FREEMA with the canonical fields, and returns the
issue key to paste into the sheet.

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
`1pIfKg0ImwvPHDCxP7-ueFh4FM3d3wYNJaK0Iqll07Dk`, tab `Resultados`). If Drive access is available,
resolve module, flow and form criticality from it; otherwise ask the tester and map the module
through the canonical table below.

## Intake

Collect from the tester's free-text report (ask only for what is missing):

- Form (`frmXXX...`) or flow number, plus the module if the form alone is ambiguous.
- What happened, and what the tester expected.
- Environment and test date.
- Evidence available (screenshots, data used such as company/record ids).

## Labels

| Label                                                             | Rule                                                     |
| ----------------------------------------------------------------- | -------------------------------------------------------- |
| `websat-stabilization`                                            | Always                                                   |
| `qa-functional` / `qa-performance` / `qa-technical` / `qa-visual` | Exactly one, per triage                                  |
| `mod-<module>`                                                    | From the canonical table below, always                   |
| `winsat-preexisting`                                              | Only when the defect is confirmed reproducible in Winsat |

Never write a label outside this vocabulary. A typo in a label silently breaks program stats.

## Summary Convention

```text
<form> · <flow or form description> · <short symptom>
```

Spanish, one line, the form always first so tickets are searchable with `summary ~ "frmXXX"`.

Example: `frmFIM_ASI_INTEGRAR · Tratamiento de Borrador · el borrador no integra la contrapartida`

## Description Template

Ticket content is written in Spanish (Spain). Use middle dots for label·value pairs, never colons.

```markdown
## Contexto

- Módulo · <module name from sheet>
- Flujo · <flow number and name> (QA Form Inventory)
- Form · <frmXXX>
- Entorno · <environment> · build <date if known>
- Tester · <name> · <test date>

## Clasificación

- Tipo · <Funcional divergencia vs Winsat | Rendimiento | Técnico | Visual>

## Pasos

1. <step>
2. <step>

## Comportamiento Winsat (esperado)

<what Winsat does>

## Comportamiento Websat (actual)

<what Websat does>

## Evidencia

<screenshots attached, data used (empresa, registro, fechas)>

## Impacto

<blocks the flow or workaround available · form criticality from the sheet>
```

Variants by type — replace only the middle sections, keep Contexto, Clasificación, Evidencia and
Impacto always:

- **Performance:** replace Pasos/Winsat/Websat with `## Escenario` (process and volumetry) and
  `## Tiempos` (WinSat s · WebSat s · diferencia %), mirroring the sheet columns.
- **Technical:** replace the Winsat/Websat sections with `## Error observado` (message, stacktrace,
  correlation id if any).
- **Visual:** keep Pasos plus screenshots; Impacto is Low unless it hides data.

## Dedupe

Before composing the preview, search:

```text
project = FREEMA AND parent = FREEMA-4055 AND summary ~ "<form>" AND statusCategory != Done
```

On a hit, show the existing issue(s) and propose adding a comment or evidence there instead. Only
create a new issue if the user confirms it is a different defect, and link it "relates to" the
existing one.

## Canonical Module Slugs

| Sheet module              | Label                           |
| ------------------------- | ------------------------------- |
| Tablas Generales          | `mod-tablas-generales`          |
| Configuración General     | `mod-configuracion-general`     |
| Parámetros de Gestión     | `mod-parametros-de-gestion`     |
| Gestión de Stocks         | `mod-gestion-de-stocks`         |
| Compras                   | `mod-compras`                   |
| Personal                  | `mod-personal`                  |
| Gestión BPO               | `mod-gestion-bpo`               |
| Prevención de Riesgos     | `mod-prevencion-de-riesgos`     |
| CRM                       | `mod-crm`                       |
| Presupuestos y Contratos  | `mod-presupuestos-y-contratos`  |
| Gestión Cuadrantes        | `mod-gestion-cuadrantes`        |
| Gestión de Candidatos     | `mod-gestion-de-candidatos`     |
| e-Movifree                | `mod-e-movifree`                |
| Equipamiento              | `mod-equipamiento`              |
| Gestión ETT               | `mod-gestion-ett`               |
| Servicios a Beneficiarios | `mod-servicios-a-beneficiarios` |
| Ventas                    | `mod-ventas`                    |
| Finanzas                  | `mod-finanzas`                  |
| Nóminas                   | `mod-nominas`                   |
| Control de Calidad        | `mod-control-de-calidad`        |
| Control Suite Manager     | `mod-control-suite-manager`     |
| Gestión Interna           | `mod-gestion-interna`           |
| Canal Interno Freematica  | `mod-canal-interno-freematica`  |

## Process

1. Parse the tester's report; collect missing intake data.
2. Resolve module, flow and form criticality (sheet if available, otherwise ask).
3. Run the three triage questions.
4. Run the dedupe search; on a hit, follow the Dedupe section.
5. Compose summary, labels, priority and description per the sections above.
6. Show the full preview and wait for explicit confirmation.
7. Create the issue via the Atlassian MCP (`createJiraIssue`) with all Fixed Jira Coordinates;
   attempt the fix version, drop it with a warning if it does not exist.
8. Return the issue key and URL, and remind the tester to paste the key into the "Ticket Jira"
   column of the sheet row.
