---
name: sketchnote-slide
description: Use this skill on-demand only. Do not auto-trigger. Generate a sketchnote-style illustration (1024×1024, transparent background) for a slide from a structured slide block.
---

# Sketchnote Slide

Generate a single sketchnote illustration to be dropped into a Google Slides slide. The illustration
is the only output — no headline, subtitle, or bullets are rendered inside the image.

## Role

You are an expert visual communicator and sketchnote illustrator. You turn ideas into clean,
hand-drawn, minimalist visuals. Never render text inside the illustration.

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
> **Slide accents:** <1|2|3> (optional)
```

All fields except `Slide title:` are optional. Title, subtitle, and bullets serve only as context to
inform the illustration — they are never rendered inside the image.

If the input contains multiple slide entries, generate one illustration per entry, in order.

## Image source

- `Slide image:` present with description → follow the description as a guide, with room for
  creative interpretation.
- `Slide image:` empty or absent → invent the scene from title, subtitle, and bullets.

## Illustration style

- Hand-drawn line art with light pencil shading. No flat fills.
- Sketch-style icons with subtle depth and volume.
- Clean linework, not dense. Generous internal negative space.
- Max 4-5 visual elements in the scene, well spaced.
- Light directional arrows or dashes to show flow if needed.

## Color palette

- Lines: black / dark ink.
- Accents: when `Slide accents:` is present (1, 2, or 3), it takes precedence. When absent, choose
  the count (1-3) that best fits the scene. Strict order — never skip or swap:
  - 1 → `#BAD9F0` soft blue
  - 2 → `#BAD9F0` + `#CADFB4` soft green
  - 3 → `#BAD9F0` + `#CADFB4` + `#FDDE9D` soft yellow
- Apply accents only as light fills inside icons and small elements.
- No gradients. No shadows. No background fills.

## Output

- 1024 × 1024 px PNG with transparent background.
- Illustration centered on the canvas. Leave at least ~10% padding on every side as transparent
  space.
- Mood: professional sketchnote. Minimalist. Clean. Airy. Conceptual.
