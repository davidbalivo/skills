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

Cuando terminen todos los candidatos de la Fase B, elige un modelo de la línea `arena cross-judge pool` de `~/.cursor/rules/pstack-models.mdc`. Si falta la regla o esa línea, elige entre `claude-opus-5-5-xhigh` y `grok-4.7-xhigh-fast`. Prefiere una familia de modelos distinta a la del padre. Lanza un subagent juez de solo lectura con ese modelo. Ve los criterios de evaluación y los candidatos por etiqueta de ruta, puntúa cada criterio y recomienda una base con su justificación. Se ejecuta en paralelo con la lectura del padre en la Fase D, no con los propios candidatos. No lances el juez mientras los candidatos sigan escribiendo.

## Fase D: Elegir Base

Lee cada candidato de principio a fin antes de elegir.

Puntúa cada candidato según los criterios de evaluación, uno por uno, no por sensación global. Compara con el juicio cruzado. Si coincidís en la base, la elección queda confirmada. Si no, uno de los dos está sesgado o los criterios de evaluación eran ambiguos. Lee ambas justificaciones antes de decidir.

Elige como base el candidato que un futuro maintainer pueda extender con más facilidad sin romper invariantes. Si dos parecen empatados, prefiere el de límites más limpios o API más pequeña, según el Laziness Protocol.

Registra la elección y el motivo en una breve nota de síntesis junto al artefacto base, incluido el veredicto del juicio cruzado.

## Fase E: Integración

Recorre una vez más cada candidato perdedor e identifica qué merece la pena portar a la base. La señal suele ser una o dos cosas por candidato, no la mayor parte.

Incorpora cada injerto a mano, según la skill de principio **redesign-from-first-principles**. No pegues de forma mecánica. El resultado tiene que seguir siendo coherente bajo un único modelo mental.

Registra qué se injertó, de qué candidato, y qué se descartó y por qué.

Cuando los N candidatos convergen en la misma forma, es una señal fuerte de acuerdo. Anota la convergencia en el registro y entrega la forma de consenso. No hace falta injertar. Cuando los N candidatos divergen mucho, la Fase A estaba poco especificada. Reencuadra y vuelve a ejecutar en lugar de promediar la divergencia.

## Fase F: Validar

El artefacto sintetizado tiene que resistir el mismo escrutinio que cualquier otra salida, según la skill de principio **prove-it-works**.

Si la verificación revela un problema que la arena no detectó, o la Fase A estaba mal (reencuadra y vuelve a ejecutar) o un candidato lo detectó y se te pasó el injerto (vuelve a la Fase E). No lo tapes.

## Salidas

Un artefacto sintetizado. Una breve nota de síntesis junto a él que nombre la base, los injertos (con su candidato de origen), los descartes, las bajas si las hubo y el resultado de la verificación.

## Origen

Traducción de la skill `arena` de [cursor/plugins](https://github.com/cursor/plugins/blob/main/pstack/skills/arena/SKILL.md) (`pstack`), con licencia MIT. Copyright (c) 2026 Lauren Tan.
