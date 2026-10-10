---
name: triad-forge
description: Lanza 3 agentes en paralelo sobre la misma tarea, elige una base e integra en ella las mejores ideas de los otros
disable-model-invocation: true
---

# Triad Forge

Lanza 3 agentes en paralelo sobre la misma tarea. Lee cada candidato de principio a fin. Elige el más fuerte como base. Integra en él las mejores ideas de los demás. Verifica el resultado sintetizado.

## Fase A: Delimitar

Prepara una tarea común para los candidatos antes de lanzarlos:

- Define qué debe entregar cada agente. Todos reciben el mismo encargo y el mismo contexto.
- Exige que cada agente compruebe su resultado y aporte pruebas de cada afirmación verificable. Si algo no puede comprobarse, debe indicarlo.
- Fija entre tres y seis criterios de evaluación concretos para comparar los resultados en la Fase D. No incluyas esos criterios en el encargo: los candidatos solo reciben la tarea.
- Usa tres agentes con Claude Opus 5.5. Cambia el número o el modelo solo si el usuario lo pide.
- Asigna a cada agente una subcarpeta propia en el directorio temporal del repositorio. Si ese directorio no existe, créalo.

## Fase B: Desplegar

Lanza a todos los agentes para que trabajen en paralelo. Dales el mismo encargo y el mismo contexto definidos en la Fase A, y asigna a cada uno su subcarpeta.

Pide a cada agente que entregue su propuesta, una explicación breve de las alternativas que consideró y descartó, y las comprobaciones realizadas con sus resultados. Debe señalar lo que no haya podido verificar.

Espera a que terminen todos antes de pasar a la Fase C. Si alguno no entrega una propuesta, continúa con las demás y deja constancia en la nota de síntesis.

## Fase C: Revisión

Al terminar la Fase B, lanza un agente revisor con Fable 5.1. Entrégale los criterios de evaluación y las rutas de las propuestas disponibles, identificadas por candidato. Indícale que no modifique archivos.

El revisor puntúa cada propuesta criterio por criterio, recomienda una base y explica por qué. Haz tu propia lectura de la Fase D mientras trabaja, sin compartirle tu valoración antes de que termine.

## Fase D: Elegir Base

Lee por completo cada propuesta, su justificación y las pruebas que aporta. Puntúala según los criterios de evaluación de la Fase A y explica brevemente cada puntuación.

Cuando termine el revisor de la Fase C, compara su evaluación con la tuya. Si no coincidís, revisa las propuestas y las pruebas que sustentan ambas valoraciones antes de decidir.

Elige como base la propuesta que mejor cumpla los criterios. Si dos quedan igualadas, elige la que sea más fácil de mantener y ampliar.

Registra la elección, tus motivos y la recomendación del revisor en una nota de síntesis junto a la propuesta base.

## Fase E: Integración

Revisa de nuevo las propuestas que no elegiste como base. Identifica qué aportaciones mejoran la elegida según los criterios de evaluación. Incorpora solo esas aportaciones.

Para integrar una idea, adapta la base como si esa idea hubiera formado parte del diseño desde el principio. Ajusta todas las partes afectadas para que el resultado siga siendo coherente; evita pegar fragmentos sin más.

Anota qué incorporaste, de qué propuesta procede y qué descartaste, con sus motivos.

Si las propuestas coinciden en lo esencial, registra la coincidencia y entrega esa solución sin forzar cambios. Si difieren tanto que no puedes defender una base, aclara el encargo de la Fase A y repite la ronda.

## Fase F: Validar

Comprueba directamente el artefacto final después de integrar las aportaciones. Haz las verificaciones adecuadas para la tarea: ejecútalo, inspecciona el resultado o revisa el archivo final. No des por bueno el trabajo solo porque un candidato diga que lo probó o porque el código compile.

Anota en la nota de síntesis qué comprobaste, cómo lo hiciste y qué resultado obtuviste. Incluye lo que no pudiste verificar.

Si encuentras un problema, corrige la causa y vuelve a comprobar el resultado. Regresa a la Fase E si falló la integración, o a la Fase A si el encargo o los criterios de evaluación estaban mal definidos.

## Salidas

Un artefacto sintetizado. Una breve nota de síntesis junto a él que nombre la base, los injertos (con su candidato de origen), los descartes, las bajas si las hubo y el resultado de la verificación.

## Origen

Traducción de la skill `arena` de [cursor/plugins](https://github.com/cursor/plugins/blob/main/pstack/skills/arena/SKILL.md) (`pstack`), con licencia MIT. Copyright (c) 2026 Lauren Tan.
