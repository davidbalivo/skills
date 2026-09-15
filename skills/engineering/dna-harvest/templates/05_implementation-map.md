# Implementación

## Mapa

```mermaid
{flowchart LR con los componentes y fronteras del área. Aristas etiquetadas: llamada, evento, tabla
o estado compartido. Línea continua verificado, punteada inferido. Marcar consumidores no verificados.}
```

## {Componente o frontera relevante}

- Ubicación: {ruta y símbolo, formulario, endpoint, proceso o consumidor; en código monolítico,
  identificar el bloque funcional y sus condiciones, sin números de línea ni enlaces}.
- Datos: {tipos y tablas leídos/escritos con anclas, cuando aplique}.
- Relaciones: {preparación de entradas y proveedores; mecanismo y evidencia de conexiones por
  llamadas, estado compartido u orden temporal. Los consumidores van en 06_blast-radius.md}.
- Frontera comprobada: {qué se investigó, qué se conoce solo por contrato y qué queda sin verificar}.
- Diagnóstico: {logs, métricas o puntos observables disponibles, cuando aporten valor}.
- Flujo: {enlace al flujo funcional correspondiente}.
- Blast radius: {enlace al punto de cambio correspondiente de 06_blast-radius.md}.

{Repetir por frontera relevante. Cada componente del mapa tiene su sección. Consumidores y efecto de
cambiar el componente viven en 06_blast-radius.md, no aquí.}
