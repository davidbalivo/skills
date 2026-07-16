# Ticket Examples

Complete tickets as this skill composes them. Example 1 carries only the mandatory intake fields;
example 2 carries every optional field plus the extra sections. Context values come from the QA
Form Inventory sheet.

## Example 1 · Mandatory Fields Only

Functional defect. The tester gave row, current behavior and steps to reproduce, nothing else.
`Comportamiento esperado` is inferred from the report and flagged in the preview; `Condiciones`,
`Ruta` and the optional sections are omitted, and priority falls back to Medium.

**Summary**

```text
frmCM_CLIENTES · Ficha de Clientes · el NIF se borra al guardar
```

**Labels** · `qa-functional`, `mod-tablas-generales`

**Priority** · Medium

**Description**

```markdown
**Contexto**

- Módulo · Tablas Generales
- Flujo · 1.2 Clientes (QA Form Inventory)
- Formulario · frmCM_CLIENTES
- Criticidad del formulario · M
- Entorno · 280
- Fecha de ejecución · 2026-07-14
- Tipo · Funcional

**Reproducción**

1. Abrir la ficha de un cliente existente.
2. Modificar el campo "NIF" y pulsar "Guardar".
3. Reabrir la ficha del mismo cliente.

**Comportamiento actual**

El campo "NIF" aparece vacío tras guardar. El resto de cambios sí se conservan.

**Comportamiento esperado**

El campo "NIF" conserva el valor introducido tras guardar.
```

## Example 2 · All Optional Fields

Performance defect. The tester gave the mandatory fields, every optional field (expected behavior,
data used, priority) and the extras (route, evidence, impact).

**Summary**

```text
frmGM_CAB_FACTURAS · Facturación · generar la factura tarda más de 3 minutos
```

**Labels** · `qa-performance`, `mod-ventas`

**Priority** · High

**Description**

```markdown
**Contexto**

- Módulo · Ventas
- Flujo · 17.4 Factura (QA Form Inventory)
- Formulario · frmGM_CAB_FACTURAS
- Criticidad del formulario · L
- Entorno · 280
- Fecha de ejecución · 2026-07-15
- Tipo · Rendimiento

**Reproducción**

- Condiciones · empresa 12, cliente 3020, albaranes 4501-4520 (unas 200 líneas en total)
- Ruta · Ventas → Facturación → Generar Facturas

1. Seleccionar los albaranes pendientes del cliente.
2. Pulsar "Generar factura".
3. Esperar a que termine el proceso.

**Comportamiento actual**

La generación tarda 3 min 20 s de media y la pantalla queda bloqueada sin feedback hasta el final.

**Comportamiento esperado**

En Winsat el mismo proceso con los mismos albaranes tarda unos 15 s.

**Evidencia**

Mediciones del tester en 3 ejecuciones · 3 min 18 s, 3 min 22 s, 3 min 21 s. Sin errores en
consola.

**Impacto**

Bloquea el cierre diario de facturación. Sin workaround conocido.
```
