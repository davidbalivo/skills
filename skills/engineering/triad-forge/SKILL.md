---
name: triad-forge
description: Lanza 3 agentes en paralelo sobre la misma tarea, elige una base e integra en ella las mejores ideas de los otros
disable-model-invocation: true
---

# Triad Forge

Lanza 3 agentes en paralelo sobre la misma tarea. Lee cada candidato de principio a fin. Elige el más fuerte como base. Integra en él las mejores ideas de los demás. Verifica el resultado sintetizado.

## Fase A: Delimitar

Los 3 candidatos recibirán el mismo prompt, así que el prompt es el contrato.

1. Indica el artefacto que produce cada candidato.
2. Deriva la rúbrica. Indica qué significa el éxito para _esta_ tarea y conviértelo en 3-6 criterios concretos y evaluables. La rúbrica es la herramienta de quien elige en la Fase D. Los candidatos solo ven la tarea.
3. Elige los runners. Usa la línea `arena runners` de `~/.cursor/rules/pstack-models.mdc`. Si falta la regla o esa línea, usa por defecto uno en `claude-opus-5-5-xhigh` y otro en `grok-4.7-xhigh-fast`. Una entrada `auto` o `inherit-parent` en esta línea o en la del juicio cruzado significa el modelo padre, así que omite `model` para ella. Si la tool Task rechaza una entrada configurada, ejecuta ese puesto con el modelo por defecto de su familia y dilo. Las familias se identifican por prefijo: `claude-*` y `grok-*`. Si no coincide ninguna familia, usa `claude-opus-5-5-xhigh`. Si rechaza un modelo por defecto, usa el slug válido más cercano de la misma familia según su mensaje de error. Lanza más cuando la arena cubra varias direcciones de diseño. Usa el mismo modelo N veces cuando el trabajo dependa de la generación más que del criterio.
4. Asigna rutas de salida. Cada candidato escribe en su propia ubicación (un git worktree cuando sea posible, si no `/tmp/arena-<slug>/candidate-<n>/`), según la skill de principio **separate-before-serializing-shared-state**.

## Fase B: Desplegar

Lanza los N subagents en un solo mensaje con `run_in_background: true`, cada uno con la tarea, la ruta al contexto compartido, su propia ruta de salida e instrucciones para producir tanto el artefacto como una breve justificación.

Cada justificación nombra las alternativas que el candidato consideró y lo que descartó.

Si un candidato no produce salida, continúa con N-1 y anota la baja en el registro de síntesis.

## Fase C: Revisión

Cuando terminen todos los candidatos de la Fase B, elige un modelo de la línea `arena cross-judge pool` de `~/.cursor/rules/pstack-models.mdc`. Si falta la regla o esa línea, elige entre `claude-opus-5-5-xhigh` y `grok-4.7-xhigh-fast`. Prefiere una familia de modelos distinta a la del padre. Lanza un subagent juez de solo lectura con ese modelo. Ve la rúbrica y los candidatos por etiqueta de ruta, puntúa cada criterio y recomienda una base con su justificación. Se ejecuta en paralelo con la lectura del padre en la Fase D, no con los propios candidatos. No lances el juez mientras los candidatos sigan escribiendo.

## Fase D: Elegir Base

Lee cada candidato de principio a fin antes de elegir.

Puntúa cada candidato contra la rúbrica criterio a criterio, no por sensación global. Compara con el juicio cruzado. Si coincidís en la base, la elección queda confirmada. Si no, uno de los dos está sesgado o la rúbrica era ambigua. Lee ambas justificaciones antes de decidir.

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
