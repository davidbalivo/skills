# Alcance de FREEMA-1234

Este ejemplo ficticio muestra la estructura de `01-scope/result.md` cuando hace falta investigar fuera del sistema. Si no hace falta, explica el motivo en `Qué hay que investigar` e indica en `Encargos de investigación` que no hay encargos. Si hay varios temas, incluye una viñeta y un encargo por cada uno, en el mismo orden.

## Qué pide el ticket

Según el ticket, un empleado de Seguriber trabajó varias noches en marzo de 2026. El plus de nocturnidad no aparece en la nómina de ese mes, aunque sí se le pagó en febrero de 2026. La consultora pide averiguar por qué y corregir el problema.

## Qué hay que investigar

Antes de analizar el código, hay que investigar este tema fuera del sistema:

- **Convenio colectivo:** una vez confirmado cuál se aplica al empleado, averiguar qué horas considera nocturnas y cómo se calcula el plus. Esas reglas servirán después para comprobar el cálculo de la nómina.

**Sabemos**

- Según el ticket, el empleado trabaja en el sector de la seguridad privada.
- El ticket indica que el plus de nocturnidad se pagó en febrero de 2026, pero no aparece en la nómina de marzo de 2026.

**Suponemos**

- Se le aplica el convenio estatal de seguridad privada, pero el ticket no lo confirma. Esta duda queda anotada en `open-items.md`.

## Encargos de investigación

### `collective-agreements`

- **Normativo:** sí.
- **Pregunta:** según el convenio aplicable en marzo de 2026, ¿qué horas son nocturnas y cómo se calcula el plus?
- **Motivo:** estas reglas harán falta después para comprobar si el cálculo de la nómina se ajusta al convenio.
- **Fuera:** los demás pluses regulados por el convenio.
- **Puntos de partida:** texto del convenio estatal de empresas de seguridad privada publicado en el BOE y tablas salariales de 2026, si se confirma que ese es el convenio aplicable.
