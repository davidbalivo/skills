---
name: triad-forge
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger. Lanza 3 agentes en paralelo sobre la misma tarea, elige una base e integra en ella las mejores ideas de los otros"
---

# Triad Forge

Lanza 3 agentes en paralelo sobre la misma tarea. Lee cada candidato de principio a fin. Elige el más fuerte como base. Integra en él las mejores ideas de los demás. Verifica el resultado sintetizado.

## Carpeta de trabajo

Trabaja en la carpeta indicada como parámetro. Si no la recibe, crea una carpeta para la tarea en el directorio temporal del repositorio. Si ese directorio no existe, créalo.

El parámetro puede indicar la ruta completa de la carpeta de trabajo o solo la de su carpeta padre. Si solo se indica la carpeta padre, o no se indica ninguna ruta, usa el nombre que corresponda de la [tabla de carpetas por tema](folder-names.md). Si varias categorías encajan, elige la más específica.

Si se indica otro nombre, úsalo. Si el tema no aparece en la tabla, usa un nombre corto en inglés con formato `kebab-case`.

```text
{carpeta}/
├── assignment.md
├── criteria.md
├── candidate-a/
├── candidate-b/
├── candidate-c/
├── reviewer/
│   └── review.md
├── result.md
└── synthesis.md
```

- `assignment.md`: el encargo común a los candidatos.
- `criteria.md`: los criterios de evaluación. Escríbelo al final, en la sección Salidas, para que ningún candidato pueda leerlo mientras trabaja.
- `candidate-a/`, `candidate-b/`, `candidate-c/`: una subcarpeta para que cada candidato guarde su propuesta.
- `reviewer/review.md`: la evaluación del revisor.
- `result.md`: el artefacto final. Si el artefacto no es un documento, `result.md` indica dónde está y qué contiene.
- `synthesis.md`: la nota de síntesis.

## Fase A: Delimitar

Prepara una tarea común para los candidatos antes de lanzarlos:

- Define qué debe entregar cada agente. Todos reciben el mismo encargo y el mismo contexto. Escríbelo en `assignment.md`.
- Exige que cada agente compruebe su resultado y aporte pruebas de cada afirmación verificable. Si algo no puede comprobarse, debe indicarlo.
- Fija entre tres y seis criterios de evaluación concretos para comparar los resultados en la Fase D. No incluyas esos criterios en el encargo: los candidatos solo reciben la tarea. No los escribas en ningún archivo hasta llegar a la sección Salidas.
- Usa tres agentes con Claude Opus 5.5. Cambia el número o el modelo solo si el usuario lo pide.

## Fase B: Desplegar

Lanza a todos los agentes para que trabajen en paralelo. Dales el encargo de `assignment.md` y asigna a cada uno su propia subcarpeta.

Pide a cada agente que entregue su propuesta, una explicación breve de las alternativas que consideró y descartó, y las comprobaciones realizadas con sus resultados. Debe señalar lo que no haya podido verificar.

Espera a que terminen todos antes de pasar a la Fase C. Si alguno no entrega una propuesta, continúa con las demás y deja constancia en `synthesis.md`.

## Fase C: Revisión

Al terminar la Fase B, lanza un agente revisor con Fable 5.1. Dale los criterios de evaluación en su encargo, no en un archivo, y las rutas de las propuestas disponibles, identificadas por candidato. Indícale que escriba su evaluación en `reviewer/review.md` y que no modifique ningún otro archivo.

El revisor puntúa cada propuesta criterio por criterio, recomienda una base y explica por qué. Haz tu propia lectura de la Fase D mientras trabaja, sin compartirle tu valoración antes de que termine.

## Fase D: Elegir Base

Lee por completo cada propuesta, su justificación y las pruebas que aporta. Puntúala según los criterios de evaluación de la Fase A y explica brevemente cada puntuación.

Cuando termine el revisor de la Fase C, compara su evaluación con la tuya. Si no coincidís, revisa las propuestas y las pruebas que sustentan ambas valoraciones antes de decidir.

Elige como base la propuesta que mejor cumpla los criterios. Si dos quedan igualadas, elige la que sea más fácil de mantener y ampliar.

Registra la elección, tus motivos y la recomendación del revisor en `synthesis.md`.

## Fase E: Integración

Parte de la propuesta base para construir el artefacto final. Revisa de nuevo las propuestas que no elegiste como base. Identifica qué aportaciones mejoran la elegida según los criterios de evaluación. Incorpora solo esas aportaciones.

Para integrar una idea, adapta la base como si esa idea hubiera formado parte del diseño desde el principio. Ajusta todas las partes afectadas para que el resultado siga siendo coherente; evita pegar fragmentos sin más.

Anota en `synthesis.md` qué incorporaste, de qué propuesta procede y qué descartaste, con sus motivos.

Si las propuestas coinciden en lo esencial, registra la coincidencia y entrega esa solución sin forzar cambios. Si difieren tanto que no puedes defender una base, aclara el encargo de la Fase A y repite la ronda.

## Fase F: Validar

Comprueba directamente el artefacto final después de integrar las aportaciones. Haz las verificaciones adecuadas para la tarea: ejecútalo, inspecciona el resultado o revisa el archivo final. No des por bueno el trabajo solo porque un candidato diga que lo probó o porque el código compile.

Anota en `synthesis.md` qué comprobaste, cómo lo hiciste y qué resultado obtuviste. Incluye lo que no pudiste verificar.

Si encuentras un problema, corrige la causa y vuelve a comprobar el resultado. Regresa a la Fase E si falló la integración, o a la Fase A si el encargo o los criterios de evaluación estaban mal definidos.

## Salidas

Escribe ahora `criteria.md` con los criterios de evaluación de la Fase A. Entrega `result.md`, `synthesis.md` y `criteria.md` en la carpeta de trabajo. `synthesis.md` debe incluir:

- Una tabla con cada rol, su carpeta y el ID exacto del modelo que usó, con el nivel de esfuerzo si no es el predeterminado.
- Qué propuesta elegiste como base y por qué, y qué recomendó el revisor.
- Qué aportaciones integraste y de qué propuesta proceden; cuáles descartaste y por qué.
- Si algún agente no entregó su propuesta o si las propuestas coincidieron en lo esencial.
- Qué comprobaste sobre el resultado final, con sus resultados y lo que quedó sin verificar.
