# Ticket Content

## Labels

| Label                                                             | Rule                                            |
| ----------------------------------------------------------------- | ----------------------------------------------- |
| `qa-functional` / `qa-performance` / `qa-technical` / `qa-visual` | Exactly one, per triage                         |
| `mod-<module>`                                                    | From [module-slugs.md](module-slugs.md), always |

Never write a label outside this vocabulary. A typo in a label silently breaks program stats.

## Summary

```text
<form> · <flow or form description> · <short symptom>
```

One line, the form always first so tickets are searchable with `summary ~ "frmXXX"`.

Example: `frmFIM_ASI_INTEGRAR · Tratamiento de Borrador · el borrador no integra la contrapartida`

## Description

Use middle dots for label·value pairs, never colons.

```markdown
**Contexto**

- Módulo · <module name from sheet>
- Flujo · <flow number and name> (QA Form Inventory)
- Form · <frmXXX, canonical name from Flujos de Test>
- Criticidad del formulario · <L/M/S, from Flujos de Test>
- Entorno · <environment> · build <date if known>
- Tipo · <Funcional divergencia vs Winsat | Rendimiento | Técnico | Visual>

**Pasos**

1. <step>
2. <step>

**Comportamiento Winsat (esperado)**

<what Winsat does>

**Comportamiento Websat (actual)**

<what Websat does>

**Evidencia**

<screenshots attached, data used (empresa, registro, fechas)>

**Impacto**

<blocks the flow or workaround available>
```

Contexto, Evidencia and Impacto stay in every ticket. Only the middle section changes:

| Type        | Middle section(s)                                                       |
| ----------- | ----------------------------------------------------------------------- |
| Funcional   | Pasos, Comportamiento Winsat (esperado), Comportamiento Websat (actual) |
| Rendimiento | Escenario, Tiempos                                                      |
| Técnico     | Error observado                                                         |
| Visual      | Pasos                                                                   |

Notes:

- Rendimiento's Tiempos mirrors the sheet columns (WinSat s · WebSat s · diferencia %).
- Técnico's Error observado includes message, stacktrace, correlation id if any.
- Visual's Impacto defaults to Low unless it hides data.
