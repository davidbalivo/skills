---
name: at2h-open-items
description: "Solo para AT2H. Define cómo registrar y actualizar open items en at2h/{ticket}/open-items.md."
---

# AT2H Open Items

Usa esta skill para registrar cuestiones que todavía hay que investigar o aclarar para resolver un ticket AT2H. `at2h/{ticket}/open-items.md` permite ver su estado de un vistazo y conserva el motivo de cada cuestión.

## Cuándo registrar una cuestión

- Añade una cuestión si falta una respuesta concreta que afecta a la resolución del ticket. No dupliques una pregunta ya registrada ni un encargo de investigación, salvo que haga falta una aclaración independiente.
- Usa el siguiente ID `OI-n` disponible. No reutilices identificadores aunque una cuestión se resuelva o se descarte.
- Si la respuesta ya consta en una fuente consultada, incorpórala al resultado del paso en lugar de dejar una cuestión abierta.

## Formato

Empieza con `# Cuestiones abiertas de {ticket}`. Tras el título, mantén una tabla con `ID`, `Asunto` y `Estado`. Enlaza cada ID con su sección de detalle y conserva ese encabezado para no romper el enlace. Si todavía no hay cuestiones, deja la tabla sin filas.

Cada detalle tiene un encabezado `## OI-n: {asunto breve}` e indica el paso donde se abrió, qué falta y por qué importa. Conserva la pregunta original cuando cambie el estado.

## Estados

- `abierta`: falta la respuesta y nadie está investigando la cuestión.
- `en curso`: se está investigando o se ha pedido la aclaración.
- `resuelta`: la respuesta está anotada en el detalle, con su evidencia o una referencia al resultado que la contiene.
- `descartada`: ya no hace falta resolverla; anota el motivo en el detalle.

Actualiza el estado solo en la tabla. Si una investigación se detiene sin responder la cuestión, vuelve a dejarla `abierta`.

## Ejemplo completo

El siguiente archivo es un ejemplo ficticio pero realista de `at2h/FREEMA-1234/open-items.md`. Muestra el archivo completo con una cuestión resuelta y otra abierta; no copies los datos del caso.

```md
# Cuestiones abiertas de FREEMA-1234

| ID                               | Asunto             | Estado   |
| -------------------------------- | ------------------ | -------- |
| [OI-1](#oi-1-convenio-aplicable) | Convenio aplicable | resuelta |
| [OI-2](#oi-2-fecha-de-recalculo) | Fecha de recálculo | abierta  |

## OI-1: Convenio aplicable

- **Paso:** `01-scope`.
- **Qué falta:** confirmar qué convenio se aplica al empleado en marzo de 2026.
- **Por qué importa:** determina qué reglas de nocturnidad hay que consultar.
- **Respuesta:** se aplica el convenio estatal de seguridad privada.
- **Evidencia:** comentario de la consultora en el ticket FREEMA-1234 del 12/10/2026.

## OI-2: Fecha de recálculo

- **Paso:** `01-scope`.
- **Qué falta:** confirmar si la nómina de marzo se recalculó después del pago inicial.
- **Por qué importa:** permite saber qué ejecución hay que revisar en el siguiente paso.
```
