# Alcance de FREEMA-1234

Este ejemplo ficticio muestra la estructura de `01-scope/result.md` cuando hace falta investigar fuera del sistema. Si no hace falta, conserva las dos partes y explica el motivo en el detalle técnico, sin añadir encargos. Si hay varios temas, añade un bloque técnico por cada uno.

## Explicación funcional

### Qué pide el ticket

Según el ticket, un empleado de Seguriber trabajó varias noches en marzo de 2026. El plus de nocturnidad no aparece en la nómina de ese mes, aunque sí se le pagó en febrero de 2026. La consultora pide averiguar por qué y corregir el problema.

### ¿Hace falta investigar fuera del sistema?

Sí. Hay que consultar el convenio colectivo aplicable para saber qué horas considera nocturnas y cómo se calcula el plus. El ticket no identifica ese convenio, así que hay que confirmarlo. Sus reglas servirán después para comprobar el cálculo de la nómina.

**Sabemos**

- Según el ticket, el empleado trabaja en el sector de la seguridad privada.
- El ticket indica que el plus de nocturnidad se pagó en febrero de 2026, pero no aparece en la nómina de marzo de 2026.

**Suponemos**

- Se le aplica el convenio estatal de seguridad privada, pero el ticket no lo confirma. Esta duda queda anotada en `open-items.md`.

## Detalle técnico

### `collective-agreements`

- **Normativo:** sí.
- **Pregunta:** según el convenio aplicable en marzo de 2026, ¿qué horas son nocturnas y cómo se calcula el plus?
- **Motivo:** estas reglas harán falta después para comprobar si el cálculo de la nómina se ajusta al convenio.
- **Fuera:** los demás pluses regulados por el convenio.
- **Puntos de partida:** texto del convenio estatal de empresas de seguridad privada publicado en el BOE y tablas salariales de 2026, si se confirma que ese es el convenio aplicable.
