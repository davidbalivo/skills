---
name: at2h-out-of-scope
description: "Solo para AT2H. Define cuándo y cómo registrar hallazgos criticos fuera de scope en at2h/{ticket}/out-of-scope.md."
---

# AT2H Out of Scope

Usa esta skill para registrar en `at2h/{ticket}/out-of-scope.md` problemas graves ajenos al ticket que aparezcan de forma incidental durante el trabajo. No busques problemas adicionales para llenar este archivo.

## Criterio

Registra un hallazgo solo si hay indicios concretos de un impacto grave y el problema queda fuera del ticket. Valora su gravedad según el contexto, sin limitarte a una lista de tipos de fallo. No registres una sospecha sin evidencia ni un problema que forme parte del ticket.

Anota lo observado y por qué merece atención independiente. No amplíes la investigación ni corrijas el problema dentro de este paso.

## Formato

Empieza con `# Hallazgos fuera de alcance de {ticket}`. Si no hay hallazgos, indica `Sin hallazgos críticos por ahora.` Sustituye esa frase cuando registres el primero.

Cada hallazgo lleva el siguiente ID `OOS-n` disponible, sin reutilizar identificadores, y una sección `## OOS-n: {asunto breve}` con:

- **Paso:** dónde se detectó.
- **Qué ocurre:** el hecho observado, sin atribuirle una causa no comprobada.
- **A quién afecta:** personas, datos o procesos afectados, si se sabe.
- **Evidencia:** referencia concreta a lo observado.
- **Por qué es crítico:** impacto grave que justificó registrarlo aparte.

Evita duplicados. Si ya existe una entrada sobre el mismo problema, complétala con la evidencia nueva.

## Ejemplo completo

El siguiente archivo es un ejemplo ficticio pero realista de `at2h/FREEMA-1234/out-of-scope.md`. Muestra el archivo completo con un hallazgo; el caso no define qué problemas debes buscar.

```md
# Hallazgos fuera de alcance de FREEMA-1234

## OOS-1: Adjunto de otro cliente en el ticket

- **Paso:** `01-scope`.
- **Qué ocurre:** el ticket sobre el plus de nocturnidad incluye una nómina de otro cliente.
- **A quién afecta:** empleados del otro cliente cuyos datos figuran en el adjunto.
- **Evidencia:** adjunto `nomina-cliente-b-marzo.pdf` del ticket FREEMA-1234.
- **Por qué es crítico:** personas con acceso al ticket pueden ver datos salariales ajenos al caso.
```
