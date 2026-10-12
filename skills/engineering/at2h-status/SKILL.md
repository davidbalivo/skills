---
name: at2h-status
description: "Solo para AT2H. Define cómo crear, leer y actualizar status en at2h/{ticket}/status.md."
---

# AT2H Status

Usa esta skill cuando un paso de AT2H cree, consulte o actualice `at2h/{ticket}/status.md`. El archivo registra los eventos de cada paso; no sustituye al resultado de ese paso.

## Formato

Al crear el archivo, escribe la cabecera y la línea separadora de la tabla. Añade solo eventos reales y conserva siempre las filas anteriores.

- `Date`: fecha y hora del evento en formato `YYYY-MM-DD HH:mm`.
- `Step`: ruta del directorio del paso relativa a `at2h/{ticket}/`.
- `Event`: `start` al comenzar, `done` al terminar o `stopped` cuando no se puede continuar.
- `Note`: motivo concreto de `stopped`; vacío en `start` y `done`.

## Actualización

- Lee la última fila de cada `Step` para conocer su estado. La última fila del archivo solo indica el evento más reciente; puede haber varios pasos en curso a la vez.
- Si un paso quedó en `stopped` y se reanuda, añade otro `start`. Si ya está en `start`, continúa sin repetirlo. No añadas `done` hasta que el paso haya terminado.
- No edites ni borres filas anteriores. Si trabajan subagentes en paralelo, una sola sesión añade las filas.

## Ejemplo completo

El siguiente archivo es un ejemplo ficticio pero realista de `at2h/FREEMA-1234/status.md`. Muestra el archivo completo después de reanudar y terminar una investigación; las fechas y los pasos son solo ilustrativos.

```md
| Date             | Step                              | Event   | Note                        |
| ---------------- | --------------------------------- | ------- | --------------------------- |
| 2026-10-11 10:02 | 01-scope                          | start   |                             |
| 2026-10-11 10:45 | 01-scope                          | done    |                             |
| 2026-10-11 11:00 | 02-research/collective-agreements | start   |                             |
| 2026-10-11 11:52 | 02-research/collective-agreements | stopped | Falta confirmar el convenio |
| 2026-10-12 09:10 | 02-research/collective-agreements | start   |                             |
| 2026-10-12 10:05 | 02-research/collective-agreements | done    |                             |
```
