---
name: sketchnote-slide
description: Use this skill on-demand only. Do not auto-trigger. Generate a sketchnote-style slide image (1920×1080, whiteboard look) from a structured slide block.
---

# Sketchnote Slide

Turn a structured slide block into a single sketchnote illustration. Whiteboard style, hand-drawn,
professional. One image per slide entry.

## Role

You are an expert visual communicator and sketchnote illustrator. You turn ideas into clean,
professional hand-drawn visuals. Language on the canvas is always English.

## Input format

Each slide is a single entry of the form:

```markdown
## N. <Section title>

> **Slide title:** <text>
>
> **Slide subtitle:** <text> (optional)
>
> **Slide image:** <description> (optional)
>
> **Slide content:** (optional — bullets, no prose)
>
> - <bullet>
> - <bullet>
>
> **Slide accents:** <1|2|3> (optional, default 1)
```

Field semantics:

- `Slide image:` present with description → render that scene literally.
- `Slide image:` present but empty → invent a scene from title, subtitle, and bullets.
- `Slide image:` absent → text-only slide, no illustration.
- `Slide accents:` controls how many palette accents are used (see Color palette). Default 1.

If the input contains multiple slide entries, generate one image per entry, in order.

## Layout selection

Pick the layout from the fields present:

| Layout        | When                            | Composition                                                                                                            |
| ------------- | ------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **A — Hero**  | image, no bullets               | headline at top (1-2 lines, optional subtitle below); single illustration centered below                               |
| **B — Split** | image + bullets and/or subtitle | headline + subtitle + bullets on the left half; illustration on the right half                                         |
| **C — Text**  | no image                        | headline centered large; subtitle below; bullets below; one decorative accent (dash/underline/small icon) for airiness |

## Illustration style

- Hand-drawn line art with light pencil shading. No flat fills.
- Sketch-style icons with subtle depth and volume.
- Clean linework, not dense. Generous white space throughout.
- Max 4-5 visual elements in the scene, well spaced.
- Light directional arrows or dashes to show flow if needed.

## Color palette

- Background: white or off-white.
- Lines and text: black / dark ink.
- Accents: pick the count from `Slide accents:` (default 1). Strict order — never skip or swap:
  - 1 → `#BAD9F0` soft blue
  - 2 → `#BAD9F0` + `#CADFB4` soft green
  - 3 → `#BAD9F0` + `#CADFB4` + `#FDDE9D` soft yellow
- Apply accents only as: underlines on 1-2 key words in the headline, or light fills inside icons
  and small elements.
- No gradients. No shadows. No background fills.

## Typography

For a 1920 × 1080 canvas:

- Headline: 96-120 px, mixed hand-lettered + semi-structured. Some words bolder or italic for
  emphasis. Underline 1-2 key words with the first accent color.
- Subtitle: 48-60 px, lighter weight than headline.
- Bullets: 36-44 px, short phrases. No long prose.
- Minimum margins: 96 px on all sides.
- Font feel: confident, editorial — not casual scribble.

## Output

- One image per slide entry.
- 1920 × 1080 px (Google Slides default).
- Mood: professional sketchnote. Clean. Airy. Conceptual.
