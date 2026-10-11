---
name: at2h-scope
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger. Primer paso de AT2H. Lee un ticket de Jira, define con triad-forge qué investigar para resolverlo y presenta el alcance para confirmación humana"
---

# AT2H Scope

A partir de la clave de un ticket de Jira, crea su carpeta y usa triad-forge para definir qué hace falta investigar. El proceso se completa cuando una persona confirma el alcance.

Da por hecho que la persona que debe confirmar el alcance no conoce el código ni el dominio. Escribe en lenguaje funcional sencillo todo lo que deba leer o confirmar.

## Reglas

- No escribas nunca en la base de datos de un cliente. Consúltala solo en modo lectura.
- Al redactar explicaciones funcionales, aplica la skill `unslop` si está instalada. Si escribes en español, aplica también `spanish-artifacts` si está instalada.
- Anota en `out-of-scope.md` los hallazgos críticos ajenos al ticket, sin investigarlos ni corregirlos. Se consideran críticos los que puedan causar pérdida o corrupción de datos, importes o nóminas incorrectos, incumplimiento legal o fiscal, o un fallo de seguridad.

## Carpeta del ticket

Trabaja en `at2h/{ticket}/`, en la raíz del repositorio actual. `{ticket}` es la clave de Jira, por ejemplo `FREEMA-1234`.

```text
at2h/{ticket}/
├── status.md
├── open-items.md
├── out-of-scope.md
└── 01-scope/
```

- `status.md`: el historial de pasos del ticket. Ver [Status](#status).
- `open-items.md`: las cuestiones que quedan por investigar o aclarar para resolver el ticket. Cada entrada indica en qué paso se abrió, qué falta, por qué importa y su estado: `abierta`, `en curso`, `resuelta` o `descartada`.
- `out-of-scope.md`: hallazgos críticos ajenos al ticket. Cada entrada indica el paso que lo detecta, qué ocurre y a quién afecta, la evidencia y por qué es crítico.
- `01-scope/`: la carpeta de trabajo de triad-forge para este paso.

## Status

`status.md` es una tabla a la que solo se añaden filas. Nunca edites ni borres filas anteriores. La última fila muestra el evento más reciente; hay un paso en curso solo si ese evento es `start`.

```md
| Date             | Step                  | Event   | Note                    |
| ---------------- | --------------------- | ------- | ----------------------- |
| 2026-10-11 10:02 | 01-scope              | start   |                         |
| 2026-10-11 10:45 | 01-scope              | done    |                         |
| 2026-10-11 11:00 | 02-investigation/code | start   |                         |
| 2026-10-11 11:52 | 02-investigation/code | stopped | Falta acceso a los logs |
```

- `Step`: la carpeta del paso, por ejemplo `01-scope` o `02-investigation/code`.
- `Event`: solo `start`, `stopped` o `done`.
- `Note`: el motivo de `stopped`; se deja vacío en `start` y `done`.

## Fase A: Preparar

Si `at2h/{ticket}/` no existe, crea la carpeta con sus tres archivos y `01-scope/`. Añade a `status.md` el evento `start` de `01-scope`.

Si la carpeta ya existe, lee `status.md`. Si `01-scope` ya tiene el evento `done`, detente e indica cuál es el siguiente paso. Si su último evento es `stopped`, revisa el motivo y añade un nuevo `start` al reanudarlo. Si su último evento es `start`, continúa desde donde se dejó.

Lee el ticket completo en Jira, incluidos el título, la descripción, los comentarios y los adjuntos. Si falta información para entender qué falla o qué se pide, detente. Explica en lenguaje funcional qué falta y añade a `status.md` el evento `stopped` de `01-scope` con el motivo en `Note`.

## Fase B: Definir el alcance

Ejecuta `triad-forge` con `01-scope/` como carpeta de trabajo. El encargo es delimitar la investigación necesaria para resolver el ticket. Deja claro que en esta fase no se buscan causas, no se formulan hipótesis ni se proponen soluciones.

Cada candidato debe:

- Revisar solo las fuentes necesarias para decidir dónde investigar.
- Identificar el contexto imprescindible, como el cliente, el pod, la base de datos, el entorno o la versión. Buscarlo primero en el ticket y después, si hace falta, en el código u otras fuentes. Anotar en `open-items.md` los datos necesarios que no pueda identificar.
- Definir solo las líneas de investigación necesarias para el ticket. Ejemplos: `code` (código), `data-map` (base de datos), `logs` (registros) y `research` (normativa, formatos y APIs).
- Para cada línea, indicar qué pregunta debe responder, por qué importa, qué queda fuera y los puntos de partida conocidos.

`result.md` debe tener dos partes:

- Una explicación funcional para alguien sin contexto: qué pide el ticket, qué se investigará, por qué y qué queda fuera. Aplica las skills indicadas en las reglas.
- El detalle técnico: contexto identificado, resultados de la exploración y un encargo por línea de investigación para `at2h-investigate`.

En ambas partes, distingue los hechos de las suposiciones.

## Fase C: Confirmar

Muestra la explicación funcional de `result.md` a la persona que debe confirmar el alcance. Pregúntale si la investigación prevista responde a lo que pide el ticket. Espera su respuesta.

Si pide ajustes que cambian las líneas de investigación, vuelve a la Fase B e incorpóralos. Si los cambios solo afectan a la redacción, corrige `result.md`.

Cuando confirme el alcance, añade a `status.md` el evento `done` de `01-scope`.

## Salidas

- `at2h/{ticket}/` con `status.md`, `open-items.md` y `out-of-scope.md`.
- `01-scope/` con las salidas de triad-forge: `result.md`, `synthesis.md` y `criteria.md`.
- `status.md` con el evento `done` de `01-scope`, o `stopped` y su motivo si se detuvo.
