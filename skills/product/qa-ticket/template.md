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
- Tipo · <Funcional | Rendimiento | Visual>

**Reproducción**

- Condiciones · <test data, data volume and initial state if relevant>
- Ruta · <Menú 1 → Menú 2 → Pantalla; omit if not applicable>

1. <single action using exact visible labels>
2. <single action using exact visible labels>

**Comportamiento esperado**

<expected result or acceptable behavior; mention Winsat only when the tester uses it as reference>

**Comportamiento actual**

<actual result, elapsed time, error or visual difference observed in Websat>

**Evidencia**

<screenshots, measurements, logs, stacktrace, correlation id, or pending manual image upload>
```

Keep test data, data volume and initial state in `Condiciones`. Use `Evidencia` only for supporting
artifacts and technical diagnostics; do not repeat the test data there.

For `Ruta`, join exact visible navigation labels with `→`; omit it when no navigation is needed.
Write one action per numbered step, in Spanish infinitive, and quote exact controls and values. Keep
expected and current results in their behavior sections.

### Optional Impact

Include this section only when the tester provides an impact or workaround, either in the original
report or after accepting the optional prompt:

```markdown
**Impacto**

<blocks the flow, affects the user or has a workaround>
```

If the tester declines or provides no impact, omit the heading and section completely.

### Evidence Handling

When an image is available only in the conversation and has no stable URL that Jira can use, write:

```markdown
**Evidencia**

Captura pendiente de adjuntar manualmente por el tester.
```

Do not claim that the image is attached. Warn the tester in the preview and remind them after issue
creation that they must upload it manually in Jira.

## Type-Specific Content

| Type        | Fill rule                                                                                                                                                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Functional  | Describe the expected outcome and the incorrect current outcome. Put error messages in current behavior and raw technical diagnostics in Evidence.                                                                             |
| Performance | Use the test context and measurements the tester provides. Ask only for missing facts needed to understand and reproduce the delay; do not require volume, repetitions or a Winsat measurement by default.                    |
| Visual      | Describe the expected and current presentation and record the available evidence. When an image requires manual upload, follow Evidence Handling. Never infer Low priority solely because the defect is visual.                 |
