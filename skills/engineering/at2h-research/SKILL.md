---
name: at2h-research
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger. Segundo paso de AT2H. Investiga los temas fuera del sistema que pide el alcance, un subagente por tema, y deja lo que necesita la hipótesis"
---

# AT2H Research

A partir de la clave de un ticket con el alcance confirmado, investiga los temas fuera del sistema que pide `01-scope/result.md`, como una normativa o un convenio. Lanza un subagente por tema. Los temas normativos usan triad-forge. El proceso termina cuando todos los temas terminan o se detienen.

Da por hecho que la persona que lee los resultados no conoce el código ni el dominio. Explica en términos funcionales y sencillos todo lo que deba leer.

## Reglas

- Investiga solo fuera del sistema. No analices el código, la base de datos ni los logs; eso corresponde a `at2h-hypothesis`.
- No busques la causa del problema, no formules hipótesis ni propongas soluciones. Limítate a responder la pregunta de cada encargo.
- Indica la fuente de cada regla, valor o formato: documento, artículo o apartado, enlace y fecha de consulta. Prefiere fuentes oficiales. Si una respuesta depende de la fecha, indica desde y hasta cuándo es válida.
- Al redactar explicaciones funcionales, aplica la skill `unslop` si está instalada. Si escribes en español, aplica también `spanish-artifacts` si está instalada.
- Solo la sesión principal escribe en `status.md`, `open-items.md` y `out-of-scope.md`. Los subagentes le devuelven lo que haya que anotar.

## Carpeta del ticket

Trabaja en `at2h/{ticket}/`, en la raíz del repositorio actual, en la rama `{ticket}`.

```text
at2h/{ticket}/
├── status.md
├── open-items.md
├── out-of-scope.md
├── 01-scope/
│   └── result.md
└── 02-research/
    ├── collective-agreements/
    └── {tema}/
```

- `01-scope/result.md`: la decisión del alcance, con un encargo por tema en `Encargos de investigación`.
- `02-research/{tema}/`: la carpeta de trabajo de cada tema. Con triad-forge contiene sus salidas; sin triad-forge, solo `result.md`.
- `status.md`: historial de eventos de los pasos. Usa `at2h-status`.
- `open-items.md`: cuestiones pendientes de investigar o aclarar. Usa `at2h-open-items`.
- `out-of-scope.md`: hallazgos graves ajenos al ticket. Usa `at2h-out-of-scope`.

## Fase A: Preparar

Cámbiate a la rama `{ticket}`. Si no existe, detente e indica que antes hay que ejecutar `at2h-scope`.

Lee `status.md`. Si `01-scope` no tiene el evento `done`, detente e indica que antes hay que ejecutar `at2h-scope`.

Decide qué temas investigar:

- Si recibes el nombre de un tema, investiga solo ese.
- Si recibes el ID de una entrada de `open-items.md`, crea un tema nuevo para ella. Ponle un nombre corto en inglés, usa su sección de detalle como encargo, decide si es normativo y marca la entrada `en curso` en la tabla.
- Si no recibes nada, investiga todos los temas de `Encargos de investigación` cuyo último evento no sea `done`.

Si no queda ningún tema pendiente, detente e indica que el siguiente paso es `at2h-hypothesis`.

## Fase B: Investigar

Lanza un subagente por tema, con un máximo de 4 en paralelo. Cuando uno termine, lanza el siguiente. Al lanzar cada subagente, añade a `status.md` el evento `start` de `02-research/{tema}`.

Cada subagente recibe:

- El encargo del tema: la pregunta, el motivo, qué queda fuera y los puntos de partida.
- Las reglas de esta skill.
- La ruta de [`template.md`](template.md). `result.md` sigue su estructura. Su contenido es ficticio: no se copian datos, fuentes ni valores.

Si el tema es normativo, el subagente ejecuta `triad-forge` con `02-research/` como carpeta padre y el nombre del tema como carpeta. Si no lo es, investiga solo y escribe `02-research/{tema}/result.md`.

`result.md` debe tener estas secciones, en este orden:

- `Qué se buscó`: la pregunta del encargo, en lenguaje funcional.
- `Qué se encontró`: la respuesta en lenguaje funcional sencillo, para alguien sin contexto. Después, separa los hechos de las suposiciones con los rótulos **Sabemos** y **Suponemos**.
- `Lo que necesita la hipótesis`: una tabla con cada regla, valor, formato o comportamiento esperado que la hipótesis tendrá que comparar con el código, con su fuente y su vigencia.
- `Qué no se pudo confirmar`: lo que queda abierto y por qué.
- `Fuentes`: cada fuente consultada, con enlace y fecha de consulta.

Al terminar, cada subagente devuelve a la sesión principal:

- Si el tema terminó o se detuvo, y el motivo si se detuvo.
- Un resumen funcional de lo encontrado, de tres a cinco frases.
- Las cuestiones fuera de su encargo que hagan falta para resolver el ticket.
- Los hallazgos críticos ajenos al ticket.

## Fase C: Cerrar

Con lo que devuelve cada subagente:

- Añade a `status.md` el evento `done` del tema, o `stopped` con el motivo en `Note`.
- Añade a `open-items.md` las cuestiones fuera del encargo: una fila en la tabla con estado `abierta` y su sección de detalle.
- Añade a `out-of-scope.md` los hallazgos críticos, con un identificador `OOS-n`.
- Si el tema investigaba una entrada de `open-items.md`, márcala `resuelta` solo si el resultado responde la cuestión y deja constancia de la respuesta. Si sigue sin respuesta, déjala `abierta`.

Cuando terminen todos los temas, haz un commit que incluya `at2h/{ticket}/` en la rama del ticket:

- `docs(at2h): {ticket} research`

Muestra a la persona el resumen funcional de cada tema, los temas detenidos con su motivo y las entradas nuevas de `open-items.md` y `out-of-scope.md`. Indica el siguiente paso:

- `at2h-hypothesis` si todos los temas terminaron.
- Qué falta para reanudar los temas detenidos.

## Salidas

- `02-research/{tema}/result.md` por cada tema investigado, con las salidas de triad-forge si es normativo.
- `status.md` con el evento `done` o `stopped` de cada tema.
- `open-items.md` y `out-of-scope.md` con las entradas nuevas.
- Un commit con la carpeta del ticket.
