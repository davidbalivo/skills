---
name: md-essentials
description: "You MUST use this skill when writing or modifying Markdown files — defines canonical formatting rules for structure, lists, emphasis, links, tables, and code fences."
---

# Markdown Formatting

Canonical Markdown rules for this repository. The goal is simple: write Markdown that Prettier can
format without collapsing structure or degrading readability.


## Prettier Contract

This skill is written against the following Prettier configuration. All formatting rules below
assume these exact values are in effect:

- `printWidth: 100`
- `proseWrap: "preserve"`
- `endOfLine: "lf"`

Any `.prettierrc` change that modifies these values requires re-validating every rule in this skill.


## 1. Headings

- Never skip heading levels. `##` cannot jump to `####`; the next level must be `###`.
- Always put a blank line before and after a heading.
- Never add a closing `#` at the end of a heading line.

Correct:

```md
## Section

### Subsection

Content here.
```

Incorrect:

```md
## Section

#### Subsection

Content here.
```


## 2. List markers

- `-` is the only permitted list marker. `*` and `+` are forbidden.
- For task lists: `[ ]` for pending, `[x]` for done. No other variants.

Correct:

```md
- First item
- [x] Done task
- [ ] Pending task
```

Incorrect:

```md
- First item

* [x] Done task
```


## 3. Canonical list pattern

Use real nested lists, never pseudo-lists embedded in a paragraph.

```md
- Testing:
  - First scenario.
  - Second scenario.

- Subtasks:
  - [x] First subtask.
  - [x] Second subtask.
```

Rules:

- `Testing:` and `Subtasks:` must be their own bullet.
- Their contents must be real child bullets on following lines.
- Never write multiple test cases inline after `- Testing:`.
- Never collapse multiple subtasks into one paragraph.


## 4. Long bullet content

When a bullet is long:

- Write it as normal prose.
- Let Prettier decide wrapping.
- Avoid manual wrapping that changes semantic structure.

Preferred:

```md
- Introduce a failing test and attempt to push. The hook must fail with
  `pnpm test` output and abort the push.
```

Avoid:

```md
- Introduce a failing test, attempt to push; hook fails with `pnpm test` output and push is aborted.
```


## 5. Emphasis

- `**bold**` is mandatory for bold. `__bold__` is forbidden.
- `_italic_` is mandatory for italic. `*italic*` is forbidden.
- These match Prettier 3.x defaults: Prettier normalizes emphasis markers on `--write`.

Correct:

```md
Use **bold** for critical warnings and _italic_ for introduced terms.
```

Incorrect:

```md
Use **bold** for critical warnings and _italic_ for introduced terms.
```


## 6. Links

- Inline form `[text](url)` is the default for all links.
- Reference-style links (`[text][ref]` + `[ref]: url`) are reserved for cases where the same URL
  appears multiple times in the same document.
- Never use bare URLs without link text unless the URL itself is the content being communicated.

Correct:

```md
See the [Prettier documentation](https://prettier.io/docs) for details.
```


## 7. Tables

- Use one row per line. Never collapse multiple rows onto one line.
- Always include the separator row (`| --- |`) between the header and body.
- Write tables with minimal padding and let Prettier align columns on `--write`.
- Do not try to manually align columns to a style that differs from Prettier's output.

The output Prettier produces (write this form or let Prettier generate it):

```md
| Name  | Type   | Required |
| ----- | ------ | -------- |
| id    | string | yes      |
| email | string | yes      |
```

Incorrect (missing separator row, multiple rows collapsed):

```md
| Name | Type | Required |
| id | string | yes | email | string | yes |
```


## 8. Code fences

- A language tag is mandatory on every fenced code block. Never use a bare ` ``` ` fence.
- Always put a blank line before the opening fence and after the closing fence.

Correct — language tag present, blank lines before and after:

    ```sh
    pnpm validate
    ```

Incorrect — no language tag, no blank lines:

    ```
    pnpm validate
    ```


## 9. Blank lines

- Maximum one consecutive blank line anywhere in the document.
- Do not use multiple blank lines to add visual separation; use headings or `---` instead.
