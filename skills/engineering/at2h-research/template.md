# Investigación de FREEMA-1234: collective-agreements

Este ejemplo ficticio muestra la estructura de `02-research/{tema}/result.md`. Las fuentes, artículos y valores son inventados. Si una sección no tiene contenido, indícalo con una frase.

## Qué se buscó

Qué horas considera nocturnas el convenio que se aplica al empleado en marzo de 2026 y cómo se calcula el plus de nocturnidad.

## Qué se encontró

El convenio estatal de seguridad privada considera nocturnas las horas trabajadas entre las 22:00 y las 6:00. Por cada hora nocturna se paga un plus fijo por hora, que depende de la categoría del empleado. El convenio vigente en marzo de 2026 es el mismo que en febrero, así que la regla no cambió entre esos dos meses.

**Sabemos**

- La franja nocturna y la forma de calcular el plus, porque las fija el texto del convenio.
- El importe por hora de 2026, porque lo publican las tablas salariales.

**Suponemos**

- Que este es el convenio que se aplica al empleado. Sigue pendiente de confirmar en `open-items.md`.

## Lo que necesita la hipótesis

| Dato                        | Valor                               | Fuente                    | Vigencia                |
| --------------------------- | ----------------------------------- | ------------------------- | ----------------------- |
| Franja nocturna             | De 22:00 a 6:00                     | Convenio, artículo 00.1   | 2023-01-01 a 2026-12-31 |
| Cálculo del plus            | Importe por hora nocturna trabajada | Convenio, artículo 00.2   | 2023-01-01 a 2026-12-31 |
| Importe por hora, vigilante | 0,00 €                              | Tablas salariales de 2026 | 2026-01-01 a 2026-12-31 |
| Horas mínimas para cobrarlo | No hay mínimo                       | Convenio, artículo 00.2   | 2023-01-01 a 2026-12-31 |

## Qué no se pudo confirmar

- Si la empresa tiene un acuerdo propio que mejore el plus. No aparece en las fuentes públicas.

## Fuentes

- Convenio colectivo estatal de empresas de seguridad privada, BOE-A-0000-00000, consultado el 2026-10-12.
- Tablas salariales de 2026 del mismo convenio, BOE-A-0000-00001, consultado el 2026-10-12.
