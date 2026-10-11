# Alcance de FREEMA-1234

Ejemplo ficticio. Muestra la forma de `01-scope/result.md`, no un caso real.

## Explicación funcional

### Qué pide el ticket

Un empleado de Seguriber trabajó varias noches en marzo. En su nómina de marzo no aparece el plus de nocturnidad. En febrero sí se le pagó. La consultora pide saber por qué y corregirlo.

### Qué se va a investigar y por qué

- Cómo decide el programa cuándo pagar el plus de nocturnidad, porque ese cálculo determina si debe aparecer en la nómina.
- Qué turnos y qué configuración del plus se aplicaron al empleado en febrero y marzo, porque esos datos intervienen en el cálculo.
- Si el cálculo cambió entre febrero y marzo, porque en febrero sí se pagó el plus.

### Qué queda fuera

- Los demás pluses de la nómina.
- Otros empleados. Solo se mirarán si la investigación encuentra un motivo.

### Lo que sabemos y lo que suponemos

- Sabemos el cliente, el empleado y los periodos, porque los da el ticket.
- Suponemos que la nómina se calculó en WebSat. El ticket no lo indica, aunque el mapa de clientes registra su uso desde 2025. Hay que confirmarlo antes de profundizar en el código; queda anotado en `open-items.md`.

## Detalle técnico

### Contexto

| Dato     | Valor                  | Fuente                           | Tipo       |
| -------- | ---------------------- | -------------------------------- | ---------- |
| Cliente  | Seguriber              | Ticket                           | Hecho      |
| Pod y BD | pod01, Free_Seguriber  | Mapa de clientes                 | Hecho      |
| Empleado | 004512                 | Ticket                           | Hecho      |
| Periodo  | 2026-03                | Ticket                           | Hecho      |
| Sistema  | WebSat (por confirmar) | Mapa de clientes, uso desde 2025 | Suposición |

No hace falta identificar entorno ni versión.

### Exploración

- Una búsqueda en el código señala `src/Nomina/CalculoPluses.cs`, método `CalcularPlusesPeriodo`, como punto de partida. Aún no se ha revisado su comportamiento.
- El esquema contiene `TUR_TURNO` para los turnos y `NOM_PLUS` y `NOM_PLUS_CONVENIO` para la configuración. Aún no se han consultado registros del empleado.
- El historial de git de `CalculoPluses.cs` permite comprobar si hubo cambios entre febrero y marzo. Aún no se han analizado.

### Líneas de investigación

#### code

- **Pregunta:** ¿cómo decide el cálculo de nómina que una hora es nocturna y cuándo genera el plus?
- **Motivo:** es el proceso que debería haber añadido el plus.
- **Fuera:** el cálculo del resto de pluses.
- **Puntos de partida:** `CalculoPluses.cs`, método `CalcularPlusesPeriodo`, si se confirma el uso de WebSat.

#### data-map

- **Pregunta:** ¿qué turnos y qué configuración de nocturnidad le correspondían al empleado 004512 en febrero y marzo?
- **Motivo:** el cálculo depende de esos datos y febrero permite compararlos.
- **Fuera:** otros empleados y otros meses.
- **Puntos de partida:** `TUR_TURNO`, `NOM_PLUS`, `NOM_PLUS_CONVENIO`; empleado 004512; periodos 2026-02 y 2026-03.

#### history

- **Pregunta:** ¿hubo cambios en el cálculo de pluses entre febrero y marzo? Si los hubo, ¿cuáles?
- **Motivo:** en febrero el plus se pagó.
- **Fuera:** cambios en otros módulos.
- **Puntos de partida:** historial de git de `CalculoPluses.cs` desde el 1 de febrero de 2026.
