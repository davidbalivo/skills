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

One line, the form always first so tickets are searchable with `summary ~ "frmXXX"`. Without a
form, start with the flow.

Example: `frmFIM_ASI_INTEGRAR · Tratamiento de Borrador · el borrador no integra la contrapartida`

## Description

Use middle dots for label·value pairs, never colons.

```markdown
**Contexto**

- ID prueba · <`ID prueba` from Resultados>
- Módulo · <module name from sheet>
- Flujo · <flow number and name> (QA Form Inventory)
- Formulario · <frmXXX, `Formulario` from Flujos de Test>
- Criticidad del formulario · <L/M/S, `Sizing` from Flujos de Test>
- Entorno · <environment>
- Fecha de ejecución · <execution date from Resultados>
- Tipo · <Funcional | Rendimiento | Visual>

**Reproducción**

- Condiciones · <test data, data volume and initial state if relevant>
- Ruta · <Menú 1 → Menú 2 → Pantalla>

1. <single action using exact visible labels>
2. <single action using exact visible labels>

**Comportamiento actual**

<actual result, elapsed time, error or visual difference observed in Websat>

**Comportamiento esperado**

<expected result or acceptable behavior; mention Winsat only when the tester uses it as reference>
```

For `Ruta`, join exact visible navigation labels with `→`; include the bullet only when the tester
provides a route. Write one action per numbered step, in Spanish infinitive, and quote exact
controls and values. Keep expected and current results in their behavior sections; omit the
`Comportamiento esperado` section entirely when the tester provides no expected behavior and none
can be inferred from the report.

### Optional Sections

Append each of these sections only when the tester provides the information. Never prompt for them
(the `Evidencia` offer in Step 7 is the only exception), never invent them and never write an
empty placeholder:

```markdown
**Evidencia**

<screenshots, measurements, logs, stacktrace or correlation id>

**Impacto**

<blocks the flow, affects the user or has a workaround>
```

Use `Evidencia` only for supporting artifacts and technical diagnostics; keep error messages in
`Comportamiento actual` and never repeat the test data there.

### Evidence Handling

When an image is available only in the conversation with no stable URL that Jira can use, or when
the tester accepts the `Evidencia` offer in the preview (Step 7), write:

```markdown
**Evidencia**

Captura pendiente de adjuntar manualmente por el tester.
```

Never claim that the image is attached.
