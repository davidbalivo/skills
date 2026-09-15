# Blast radius

## {Punto de cambio}

{Qué se puede tocar: comportamiento, contrato, tabla, estado compartido o configuración. Ancla.}

- Mecanismo de propagación: {llamada, tabla o estado compartido, evento, configuración, orden
  temporal, contrato externo o datos históricos}.
- Consumidores:
  - Verificados: {quién lo usa, con ancla}.
  - Inferidos: {quién parece usarlo y por qué se infiere; omitir si no aplica}.
  - Sin verificar: {SQL dinámico, reflection, informes, integraciones o clientes sin acceso; omitir
    si no aplica}.
- Alcance: {área, otras áreas del dominio, otros dominios o externo: clientes, integraciones,
  informes, exports. Enlazar el área afectada si existe en DNA}.
- Efecto por tipo de cambio:
  - {Cambiar X}: {qué se ve afectado y por qué mecanismo}.
  - {Cambiar Y}: {qué se ve afectado y por qué mecanismo}.
- Síntomas conocidos: {cómo se manifestó una rotura anterior, con incidente o testimonio atribuido;
  omitir si no hay}.
- Verificar al cambiar: {tests existentes, comprobaciones manuales o datos a revisar,
  distinguiéndolos}.
- Implementación: {enlace a la sección correspondiente de 05_implementation-map.md}.
- Procedencia: {evidencia de los consumidores y del efecto, con sus límites}.

{Repetir por punto de cambio dentro de la cobertura. Documentar un salto hacia fuera: al cruzar a otra
área, registrar el consumidor y su alcance, sin seguir su cadena. Opcional: flowchart LR del fan-out
cuando un punto de cambio tiene varios consumidores por distintos mecanismos. Las fronteras del área
sin verificar viven en 01_about.md; aquí solo las de cada punto.}
