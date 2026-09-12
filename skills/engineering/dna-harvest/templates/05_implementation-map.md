# Implementación e impacto

## {Componente o frontera relevante}

- Ubicación: {ruta y símbolo, formulario, endpoint, proceso o consumidor; en código monolítico,
  identificar el bloque funcional y sus condiciones, sin depender solo de líneas}.
- Datos: {tipos y tablas leídos/escritos con anclas, cuando aplique}.
- Relaciones: {preparación de entradas, proveedores y consumidores; mecanismo y evidencia de
  conexiones por llamadas, estado compartido u orden temporal}.
- Frontera comprobada: {qué se investigó, qué se conoce solo por contrato y qué queda sin verificar}.
- Impacto: {qué puede verse afectado, ante qué tipo de cambio y por qué mecanismo; indicar límites de la evidencia}.
- Verificar al cambiar: {contratos, tests existentes o comprobaciones necesarias}.
- Diagnóstico: {logs, métricas o puntos observables disponibles, cuando aporten valor}.
- Flujo: {enlace al flujo funcional correspondiente}.

{Repetir por frontera relevante. Usar un diagrama ASCII con relaciones etiquetadas si aclara
acoplamientos por llamadas, eventos, datos u orden temporal. Declarar consumidores no verificados.}
