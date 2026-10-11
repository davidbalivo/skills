---
name: at2h-scope
description: Primer paso de AT2H. Lee un ticket de Jira, define con triad-forge qué hay que investigar para resolverlo y lo deja listo para confirmación humana
disable-model-invocation: true
---

# AT2H Scope

Recibe la clave de un ticket de Jira. Crea la carpeta del ticket. Define con triad-forge qué hay que investigar para resolverlo. Termina cuando una persona confirma el alcance.

Quien ejecuta esta skill no conoce el código ni el dominio. Todo lo que tenga que leer o confirmar se escribe en lenguaje funcional sencillo.

## Reglas

- Nunca escribas en la base de datos de un cliente. Solo se consulta en modo lectura.
- Las explicaciones funcionales usan la skill `unslop` si está instalada. Si están en español, usan también `spanish-artifacts` si está instalada.
- Los hallazgos críticos ajenos al ticket se anotan en `out-of-scope.md`. Un hallazgo es crítico si puede causar pérdida o corrupción de datos, importes o nóminas incorrectos, incumplimiento legal o fiscal, o un fallo de seguridad. No se investigan ni se corrigen.

## Carpeta del ticket

Trabaja en `at2h/{ticket}/`, en la raíz del repositorio actual. `{ticket}` es la clave de Jira, por ejemplo `FREEMA-1234`.

```text
at2h/{ticket}/
├── status.md
├── open-items.md
├── out-of-scope.md
└── 01-scope/
```

- `status.md`: qué pasos están hechos, cuál está en curso y cuáles faltan, con la fecha y la ruta de cada resultado.
- `open-items.md`: lo que falta por investigar o aclarar para resolver el ticket. Cada entrada indica el paso que la abre, qué falta, por qué importa y su estado: `abierta`, `en curso`, `resuelta` o `descartada`.
- `out-of-scope.md`: hallazgos críticos ajenos al ticket. Cada entrada indica el paso que lo detecta, qué ocurre y a quién afecta, la evidencia y por qué es crítico.
- `01-scope/`: la carpeta de trabajo de triad-forge para este paso.

## Fase A: Preparar

Si `at2h/{ticket}/` no existe, créala con sus tres ficheros y la carpeta `01-scope/`. Registra en `status.md` que el alcance está en curso.

Si ya existe, lee `status.md`. Si el alcance ya está confirmado, para e indica cuál es el siguiente paso. Si no, continúa desde donde se quedó.

Lee el ticket completo en Jira: título, descripción, comentarios y adjuntos. Si no tiene información suficiente para entender qué falla o qué se pide, para. Explica qué falta en lenguaje funcional y regístralo en `status.md`.

## Fase B: Definir el alcance

Ejecuta la skill triad-forge con `01-scope/` como carpeta de trabajo.

El objetivo es decidir qué hay que investigar. Bajo ningún concepto se busca la causa, se propone una solución o se formula una hipótesis. Eso corresponde a los pasos siguientes. El encargo lo deja claro y pide a cada candidato:

- Explorar lo justo para saber dónde mirar, en las fuentes que el ticket necesite: código, esquema de la base de datos, logs, documentación u otras. La exploración orienta la investigación; no explica qué ocurre.
- Identificar solo el contexto que la investigación vaya a necesitar, por ejemplo cliente, pod, base de datos, entorno o versión. Se obtiene del ticket o, si no aparece, del código u otras fuentes. Si hace falta y no se puede identificar, se anota en `open-items.md`.
- Definir las líneas de investigación que el ticket necesita, ni más ni menos. Por ejemplo:
  - `code`: en caso de que haya que investigar el código implicado.
  - `data-map`: en caso de que haya que investigar la base de datos.
  - `logs`: en caso de que sea necesario revisar logs.
  - `research`: qué dice una normativa, un formato oficial o la API de un tercero.
  - cualquiero otro tipo de investigación relevante para el ticket.
- Para cada línea de investigación, fijar la pregunta que debe responder, el motivo, qué queda fuera y los puntos de partida conocidos: ficheros, tablas, registros, documentos u otros.

`result.md` tiene dos partes:

1. Explicación funcional, para alguien sin contexto: qué pide el ticket, qué se va a investigar y por qué, y qué queda fuera. Debes utilizar las skills que se mencionan en las reglas.
2. Detalle técnico: el contexto identificado, lo que encontró la exploración y un encargo por línea de investigación, que es lo que recibe `at2h-investigate`. Incluye solo lo que aplica al ticket.

Ambas partes separan hechos de suposiciones.

## Fase C: Confirmar

Muestra la explicación funcional de `result.md` a la persona que ejecuta la skill. Pregúntale si lo que se va a investigar responde a lo que pide el ticket. Espera su respuesta.

Si pide ajustes que cambian las líneas de investigación, vuelve a la Fase B con esos ajustes. Si solo afectan a la redacción, corrige `result.md`.

Cuando confirme, registra en `status.md` que el alcance está confirmado y añade cada línea de investigación como pendiente.

## Salidas

- `at2h/{ticket}/` con `status.md`, `open-items.md` y `out-of-scope.md`.
- `01-scope/` con las salidas de triad-forge: `result.md`, `synthesis.md` y `criteria.md`.
- `status.md` con el alcance confirmado y las líneas de investigación pendientes, o con el motivo por el que se detuvo.
