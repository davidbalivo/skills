# skills

Store privado de skills personales para agentes (Claude Code y otros), instalables vía [`npx skills`](https://github.com/vercel-labs/skills).

## Prerequisitos

`npx skills` clona el repo con git, así que necesitas auth de GitHub configurada:

- **SSH:** clave SSH añadida a tu cuenta de GitHub (`ssh -T git@github.com` debe responder con éxito), o
- **HTTPS:** [`gh auth login`](https://cli.github.com/) hecho.

## Instalación

| Modalidad | Comando | Destino | Cuándo usar |
|---|---|---|---|
| Global | `npx skills add davidbalivo/skills@<skill> -g` | `~/.claude/skills/` | Skills transversales útiles en cualquier proyecto |
| Por proyecto | `npx skills add davidbalivo/skills@<skill>` | `<proyecto>/.claude/skills/` | Skills específicas del dominio del proyecto |

## Actualización

| Comando | Efecto |
|---|---|
| `npx skills update` | Actualiza todas las skills del proyecto actual |
| `npx skills update -g` | Actualiza solo las skills globales |
| `npx skills update <skill>` | Actualiza una skill concreta |

## Catálogo

| Skill | Categoría | Modalidad | Descripción |
|---|---|---|---|
| [deep-alignment](skills/discovery/deep-alignment/) | `discovery` | Global | Entrevista estructurada para alinear al agente con el usuario antes de implementar |

## Añadir una skill nueva

1. Crear carpeta `skills/<categoría>/<nombre-skill>/`. Categoría existente preferida; si es nueva, validar el nombre con propuesta 3-5.
2. Escribir `SKILL.md` con frontmatter YAML obligatorio (`name`, `description`).
3. Escribir `README.md` siguiendo la estructura: instalación primero, luego cuándo usarla e invocación.
4. Añadir fila al [catálogo](#catálogo).
5. Commit atómico: `feat(<categoría>): add <skill> skill`.
