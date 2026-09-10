---
name: dna-harvest
description: "Usar solo cuando se invoque explícitamente. Crea o actualiza el conocimiento de un área en DNA mediante investigación del código y entrevista al experto."
---

# DNA Harvest

Investigar un área, contrastar los hallazgos con el experto y documentar su conocimiento funcional
y técnico en `dna/`. Producir contexto reutilizable para personas y agentes siguiendo las
plantillas de la skill.

## Principios

- Investigar el código antes de entrevistar al experto. Formular preguntas sobre hallazgos concretos.
- Combinar significado funcional e implementación técnica dentro del área.
- Distinguir comportamiento observado, reglas esperadas y testimonio experto.
- Conservar el motivo y el ámbito de las reglas y excepciones. Registrar lo desconocido sin inventarlo.
- Acotar la captura y declarar su cobertura. En actualizaciones, revisar el contenido afectado.
- Documentar cada dato una sola vez. Priorizar explicaciones breves, anclas y diagramas que aporten claridad.

## Estructura

Crear o actualizar los artefactos en el repositorio del sistema investigado:

```text
dna/
|-- index.md                              # obligatorio
`-- domains/
    `-- <domain>/
        |-- index.md                      # obligatorio
        `-- <area>/
            |-- 01_about.md               # obligatorio
            |-- 02_vocabulary.md
            |-- 03_invariants.md
            |-- 04_flow-map.md            # obligatorio
            |-- 05_implementation-map.md  # obligatorio
            |-- 06_caveats.md
            `-- 07_unknowns.md
```

Crear los documentos sin marcar solo cuando tengan contenido. En una captura parcial, describir la
cobertura de los documentos obligatorios sin aparentar que representan toda el área.

Los índices presentan y enlazan el contenido existente. No duplicar en ellos las reglas de las áreas.
Conservar la organización y el contenido ajenos al alcance de la sesión.

## Reglas de contenido y evidencia

- Escribir en castellano y conservar la terminología técnica habitual.
- Omitir introducciones genéricas, resúmenes repetidos, narraciones línea a línea y secciones vacías.
- Usar el vocabulario para establecer el lenguaje ubicuo y relacionarlo con tipos, tablas e interfaz.
- Explicar secuencias y comportamiento en `flow-map`; localizar código, acoplamientos e impacto en
  `implementation-map`. Enlazar entre ambos cuando compartan un elemento.
- Añadir diagramas ASCII en bloques `text` cuando aclaren flujos o dependencias. Etiquetar las
  relaciones y distinguir llamadas, eventos y datos compartidos. No dibujar relaciones inferidas
  como si estuvieran verificadas.
- Toda afirmación no evidente indica procedencia: `código`, `experto`, `incidente` o `decisión`.
  Para código, citar ruta y símbolo, test, tabla o contrato; para experto, identidad o rol y fecha;
  para incidente, referencia verificable; para decisión, ADR o explicación atribuida.
- Situar la evidencia junto a la afirmación o al bloque que respalda. Una referencia genérica al
  repositorio no demuestra una afirmación concreta.
- Los tests acreditan únicamente el comportamiento que ejercitan. La lectura estática no demuestra
  ejecución en producción ni ausencia de consumidores externos.
- No convertir el comportamiento del código en una regla de negocio sin contrastarlo. Conservar
  las discrepancias explícitas y registrar lo irresuelto en `07_unknowns.md`.
- Explicar el motivo y ámbito de reglas y excepciones. Señalar cuando se desconocen.
- No enlazar DNA a SDD. Enlazar ADR solo cuando condicione el contenido. Conservar en DNA el contexto
  necesario para entender la regla sin reconstruir el historial de cambios.

## Templates

Leer solo las plantillas de los artefactos que se van a crear o modificar. Sustituir las indicaciones
entre llaves por contenido comprobado y retirar las instrucciones de plantilla del resultado.

- Índice global: [templates/root-index.md](templates/root-index.md).
- Índice de dominio: [templates/domain-index.md](templates/domain-index.md).
- Descripción del área: [templates/about.md](templates/about.md).
- Lenguaje ubicuo: [templates/vocabulary.md](templates/vocabulary.md).
- Invariantes: [templates/invariants.md](templates/invariants.md).
- Flujos: [templates/flow-map.md](templates/flow-map.md).
- Implementación e impacto: [templates/implementation-map.md](templates/implementation-map.md).
- Caveats: [templates/caveats.md](templates/caveats.md).
- Desconocidos: [templates/unknowns.md](templates/unknowns.md).

## Skills relacionadas

- Aplicar `unslop`, `md-essentials` y `spanish-artifacts` si están disponibles en el repositorio destino.
- Usar `git-operations` antes de operaciones Git si está disponible. Respetar las convenciones del
  repositorio y la autorización de la sesión para commits.

## Flujo

```text
Delimitar -> Investigar -> Borrador y huecos -> Entrevistar
                                                  |
                                                  v
Cerrar <- Transferencia <- Revision experta <- Consolidar y revisar
```

### 1. Delimitar el área

Localizar el repositorio y leer sus instrucciones. Consultar los índices DNA existentes y abrir
solo las áreas relacionadas. Identificar si se trata de una captura inicial o una actualización.

Acordar con el usuario dominio, área, alcance y fuentes accesibles. Si ya están claros en su
petición, continuar. Usar nombres existentes; confirmar nombres o límites nuevos antes de escribir.
Una sesión puede cubrir un flujo concreto dentro del área. Identificar también qué debe poder hacer
el receptor con ese conocimiento.

Si falta acceso al código, pedir su ubicación o acceso antes de preparar mapas técnicos. No inventar
la implementación a partir del nombre del área. No ampliar el alcance a todo el sistema al seguir
dependencias externas.

### 2. Investigar el sistema

Leer código, tests, formularios, APIs, procesos, eventos, esquemas y configuración pertinentes.

- Derivar el vocabulario desde tipos, tablas, campos y etiquetas de interfaz.
- Seguir entradas, llamadas, consumidores, lecturas y escrituras para reconstruir flujos.
- Identificar límites transaccionales, ordenamientos, jobs, concurrencia y datos compartidos cuando
  afecten al alcance.
- Localizar tests y puntos de diagnóstico que protejan o permitan observar el comportamiento.
- Registrar las anclas encontradas y las limitaciones de la investigación.

Registrar en `01_about.md` la fecha de investigación y la revisión del código consultada, si está
disponible. Si hay cambios locales relevantes, indicarlo: el commit por sí solo no identifica todo
el código investigado. En actualizaciones parciales, asociar esta referencia al alcance revisado.

Buscar tanto consumidores como proveedores. Una referencia ausente no prueba que el código esté
muerto: considerar configuración, ejecución dinámica e integraciones no disponibles.

En actualizaciones, verificar el contenido afectado y sus relaciones, conservando el resto.

### 3. Preparar el borrador y detectar huecos

Crear o actualizar los documentos aplicables con la evidencia disponible. Indicar en `01_about.md`
la cobertura y lo que queda sin explorar. Mantener navegables los índices global y de dominio.

Marcar el contenido nuevo o modificado pendiente de revisión junto al bloque o documento
correspondiente. Conservar las validaciones anteriores únicamente para el contenido que siga siendo
válido y no esté afectado por el cambio. En `01_about.md`, distinguir el alcance aprobado del
pendiente, tanto para la revisión experta como para la transferencia. Una actualización parcial no
renueva la validación de toda el área.

Buscar ganchos para la entrevista: literales especiales, excepciones por cliente, comentarios de
advertencia, errores ignorados, órdenes implícitos, escrituras compartidas y contradicciones.
Priorizar preguntas por impacto y por lo que impide comprender el flujo. No convertir cada detalle
técnico en una pregunta al experto.

### 4. Entrevistar al experto

Formular una pregunta por mensaje y esperar la respuesta. Presentar el hallazgo y su ancla con el
contexto mínimo necesario. Validar primero el recorrido funcional y profundizar en motivos,
invariantes, excepciones y diagnóstico.

Cuando ayude a explicar un riesgo, preguntar por un incidente real: qué ocurrió, qué señal permitió
detectarlo, qué se descartó y qué habría interpretado mal alguien nuevo. También preguntar por
dependencias operativas que no aparecen en el código.

Contrastar las respuestas con las fuentes disponibles. Si contradicen el código, distinguir lo que
debería ocurrir de lo que ocurre y pedir aclaración. No corregir código durante la captura.

Cerrar la entrevista cuando los huecos relevantes estén respondidos o reconocidos como desconocidos.
Si no hay experto disponible, conservar el borrador y los pendientes sin afirmar validación experta.
En actualizaciones sin dudas que requieran al experto, omitir esta entrevista.

### 5. Consolidar y realizar self-review

Incorporar respuestas con su procedencia y registrar los desconocidos. Revisar:

- Cobertura explícita y coherencia entre descripción, vocabulario, reglas y mapas.
- Separación entre hechos observados, expectativas y testimonios.
- Motivo y ámbito de reglas y excepciones relevantes.
- Anclas resolubles y evidencia suficiente para las afirmaciones técnicas.
- Impacto acompañado de comprobaciones concretas, sin inventar garantías o tests existentes.
- Diagramas coherentes con las fuentes y enlaces internos navegables.
- Ausencia de duplicación, relleno, placeholders y enlaces a SDD.

Corregir fallos demostrables. Volver a la entrevista si queda una contradicción que requiere
conocimiento experto. Los desconocidos reconocidos no obligan a una investigación ilimitada.

### 6. Solicitar revisión experta y resolver findings

Presentar documentos modificados, alcance, diff cuando esté disponible y pendientes. Si hay experto
disponible, pedir que revise exactitud, motivos e impacto y esperar su respuesta. Si no está
disponible, conservar el contenido sin revisar como borrador y pasar al cierre con la revisión
experta pendiente. Al retomar la sesión, comprobar si han cambiado las fuentes antes de continuar
la revisión.

Analizar cada finding con el usuario: aplicar si está respaldado; rechazar con explicación si es
incorrecto; registrar como desconocido si no puede resolverse. Revisar de nuevo las partes cambiadas
y pedir confirmación sobre ellas. No atribuir al experto una validación que no haya dado.

Registrar quién revisó, cuándo y qué alcance aprobó mediante una nota breve. Mantener los pendientes
visibles, incluso si el resto queda validado. En una corrección puramente mecánica de referencias,
comprobar las anclas sin exigir una nueva validación funcional.

### 7. Validar la transferencia

En una captura inicial, proponer un caso realista dentro del alcance para una persona sin contexto.
Pedir que use DNA para seguir el flujo, localizar la implementación, reconocer reglas, anticipar
impacto y proponer pruebas o diagnóstico. El agente prepara el caso; el receptor aporta el resultado.

Corregir los huecos que revele la prueba. Devolver al experto los cambios que alteren afirmaciones
funcionales. Si no hay receptor disponible, indicar que la transferencia queda pendiente; no
equiparar la self-review del agente con esta prueba.

En actualizaciones, repetirla cuando cambien sustancialmente flujos, reglas o impacto. Una revisión
parcial no valida todo el área.

### 8. Cerrar

Comprobar los archivos finales y resumir rutas, cobertura, revisión experta y resultado de
transferencia, señalando los pendientes. No declarar completadas validaciones no realizadas.

Si procede un commit autorizado, incluir únicamente los cambios DNA de la sesión y usar:

```text
docs(dna): document {domain}/{area}
```

Para una actualización:

```text
docs(dna): update {domain}/{area}
```

No crear commits vacíos ni hacer push como parte de la captura.
