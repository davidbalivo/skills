---
name: at2h-scope
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger. Primer paso de AT2H. Decide si hace falta investigar algo fuera del sistema antes de analizar el código y presenta la decisión para que una persona la confirme"
---

# AT2H Scope

A partir de la clave de un ticket de Jira, trabaja en su rama y prepara la carpeta del ticket. Decide si hace falta investigar algo fuera del sistema, como una normativa, antes de analizar el código. El proceso termina cuando una persona confirma la decisión.

Da por hecho que la persona que debe confirmar la decisión no conoce el código ni el dominio. Explica en términos funcionales y sencillos todo lo que deba leer o confirmar.

## Reglas

- En este paso no busques causas, no formules hipótesis ni propongas soluciones. Limítate a decidir qué hace falta investigar fuera del sistema.
- Al redactar explicaciones funcionales, aplica la skill `unslop` si está instalada. Si escribes en español, aplica también `spanish-artifacts` si está instalada.
- Si durante este paso detectas de forma incidental un problema ajeno al ticket con indicios concretos de impacto grave, anótalo en `out-of-scope.md`. No lo investigues ni lo corrijas.

## Carpeta del ticket

Trabaja en `at2h/{ticket}/`, en la raíz del repositorio actual. `{ticket}` es la clave de Jira, por ejemplo `FREEMA-1234`.

```text
at2h/{ticket}/
├── status.md
├── open-items.md
├── out-of-scope.md
└── 01-scope/
    └── result.md
```

- `status.md`: el historial de pasos del ticket. Ver [Status](#status).
- `open-items.md`: el registro de cuestiones que hay que investigar o aclarar para resolver el ticket. Tras el título, incluye una tabla con `ID`, `Asunto` y `Estado`. Cada ID `OI-n` enlaza con su sección de detalle, que indica en qué paso se abrió, qué falta y por qué importa. Usa `abierta`, `en curso`, `resuelta` o `descartada` como estado y actualízalo solo en la tabla.
- `out-of-scope.md`: hallazgos críticos ajenos al ticket. Cada entrada lleva un identificador `OOS-n` e indica en qué paso se detectó, qué ocurre, a quién afecta, la evidencia y por qué es crítico.
- `01-scope/result.md`: la decisión y, si hace falta investigar, la lista de temas y sus encargos.

## Status

`status.md` es una tabla a la que solo se añaden filas. Nunca edites ni borres filas anteriores. La última fila muestra el evento más reciente; hay un paso en curso solo si ese evento es `start`.

```md
| Date             | Step                              | Event   | Note                        |
| ---------------- | --------------------------------- | ------- | --------------------------- |
| 2026-10-11 10:02 | 01-scope                          | start   |                             |
| 2026-10-11 10:45 | 01-scope                          | done    |                             |
| 2026-10-11 11:00 | 02-research/collective-agreements | start   |                             |
| 2026-10-11 11:52 | 02-research/collective-agreements | stopped | No se encuentra el convenio |
```

- `Step`: la carpeta del paso, por ejemplo `01-scope` o `02-research/collective-agreements`.
- `Event`: solo `start`, `stopped` o `done`.
- `Note`: el motivo de `stopped`; se deja vacío en `start` y `done`.

## Fase A: Preparar

Trabaja en la rama del ticket:

- Su nombre debe ser exactamente `{ticket}`, solo la clave de Jira (por ejemplo, `FREEMA-4565`), sin descripción ni añadidos.
- Si ya existe, cámbiate a ella.
- Si no existe, créala desde la rama actual y cámbiate a ella.

Si `at2h/{ticket}/` no existe, crea la carpeta con sus tres archivos y `01-scope/`. Añade a `status.md` el evento `start` de `01-scope`.

Si la carpeta ya existe, lee `status.md`. Si `01-scope` ya tiene el evento `done`, detente e indica cuál es el siguiente paso. Si su último evento es `stopped`, revisa el motivo y añade un nuevo `start` al reanudarlo. Si su último evento es `start`, continúa desde donde se dejó.

Lee el ticket completo en Jira, incluidos el título, la descripción, los comentarios y los adjuntos. Si falta información para entender qué falla o qué se pide, detente. Explica en lenguaje funcional qué falta y añade a `status.md` el evento `stopped` de `01-scope` con el motivo en `Note`.

## Fase B: Decidir

Decide si, antes de investigar el código, la base de datos y los logs, hace falta investigar algo fuera del sistema. Abre un encargo solo si falta una respuesta externa concreta para poder formular después la hipótesis. Que un tema esté relacionado con el ticket no basta.

Si el ticket no basta para decidirlo, encarga a un agente con Claude Opus 5.5 una lectura superficial del código afectado. Su tarea es únicamente comprobar si hace falta investigar algo fuera del sistema e indicar qué ha consultado para llegar a esa conclusión. No debe profundizar en el código, buscar la causa del problema, formular hipótesis ni buscar o proponer soluciones. Usa su respuesta solo para decidir qué investigación externa hace falta.

Temas habituales:

| Tema                                                            | Carpeta                 | Normativo |
| --------------------------------------------------------------- | ----------------------- | --------- |
| Normativa legal, fiscal o laboral                               | `regulation`            | Sí        |
| Convenios colectivos                                            | `collective-agreements` | Sí        |
| Formatos oficiales: AEAT, Seguridad Social, SEPA                | `official-formats`      | Sí        |
| APIs y servicios de terceros                                    | `third-party-services`  | No        |
| Documentación interna: Confluence, manuales, tickets anteriores | `internal-docs`         | No        |

Si hace falta investigar un tema que no aparece en la tabla, asígnale un nombre corto en inglés e indica si es normativo.

Para cada tema, indica qué pregunta debe responder la investigación, por qué importa para formular la hipótesis, qué queda fuera y los puntos de partida conocidos.

`result.md` debe tener tres secciones, en este orden:

- `Qué pide el ticket`: resume el problema o la petición en lenguaje funcional sencillo para alguien sin contexto. Aplica las skills indicadas en las reglas.
- `Qué hay que investigar`: indica claramente si hace falta investigar algo fuera del sistema antes de analizar el código. Si hace falta, enumera los temas en viñetas: una por tema, con lo que hay que averiguar y por qué. Si no hace falta, explica el motivo. Usa nombres comprensibles para alguien que no conoce el código. Después, separa los hechos de las suposiciones con los rótulos **Sabemos** y **Suponemos**.
- `Encargos de investigación`: desarrolla cada tema en el mismo orden que la lista anterior, con un encargo por viñeta. Si no hay temas, explica por qué no hay encargos.

Distingue los hechos de las suposiciones en todo el documento. No incluyas encargos para temas que no aparezcan en `Qué hay que investigar`.

`result.md` sigue la estructura de [`template.md`](template.md). Su contenido es ficticio: no copies datos, nombres de ficheros ni tablas.

## Fase C: Confirmar

Muestra las secciones `Qué pide el ticket` y `Qué hay que investigar` de `result.md`. Pregunta si confirma los temas propuestos o, si no hay ninguno, la decisión de no investigar fuera del sistema. Espera su respuesta.

Si pide ajustes, corrige `result.md` y vuelve a preguntar.

Cuando confirme:

- Añade a `status.md` el evento `done` de `01-scope`.
- Haz un commit que incluya `at2h/{ticket}/` en la rama del ticket:
  - `docs(at2h): {ticket} scope`

Indica el siguiente paso:

- `at2h-research` si hay temas que investigar fuera del sistema.
- `at2h-hypothesis` si se puede pasar directamente a investigar el código, sin investigación externa.

## Salidas

- La rama `{ticket}`.
- `at2h/{ticket}/` con `status.md`, `open-items.md`, `out-of-scope.md` y `01-scope/result.md`.
- `status.md` con el evento `done` de `01-scope`, o `stopped` y su motivo si se detuvo.
- Un commit con la carpeta del ticket.
