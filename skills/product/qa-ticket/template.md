# Ticket Content

## Labels

| Label                                            | Rule                                            |
| ------------------------------------------------ | ----------------------------------------------- |
| `qa-functional` / `qa-performance` / `qa-visual` | Exactly one, per classification                 |
| `mod-<module>`                                   | From [module-slugs.md](module-slugs.md), always |

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
- Formulario · <frmXXX, canonical name from Flujos de Test>
- Criticidad del formulario · <L/M/S, from Flujos de Test>
- Entorno · <environment>
- Fecha de ejecución · <execution date from Resultados>
- Tipo · <Funcional divergencia vs Winsat | Rendimiento | Visual>

**Reproducción**

- Condiciones · <data, volumetry and initial state if relevant>

1. <step>
2. <step>

**Comportamiento Winsat (referencia)**

<result or elapsed time in Winsat>

**Comportamiento Websat (actual)**

<result, elapsed time, error or visual difference in Websat>

**Evidencia**

<screenshots, measurements, logs, stacktrace, correlation id, data used>

**Impacto**

<blocks the flow or workaround available>
```

## Type-Specific Content

| Type        | Fill rule                                                                                                                                                     |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Functional  | Describe the reference outcome and the incorrect current outcome. Put error messages in Websat and raw technical diagnostics in Evidence.                     |
| Performance | Put process, volumetry and repetitions in Conditions. Record WinSat s, WebSat s, difference s and difference % in the corresponding behavior sections.        |
| Visual      | Describe the reference and current presentation, attach comparable screenshots and state the actual user impact. Never default the impact or priority to Low. |
