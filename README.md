# Skills

A collection of agent skills that extend capabilities across planning, development, and tooling. Built for **Golden Hippo's** suite of brands.

---

## Skills

| Skill | What it does |
|-------|--------------|
| [`create-design-md`](./create-design-md/) | Builds a `DESIGN.md` design system document through a 6-step interview, conforming to the [Google DESIGN.md spec](https://github.com/google-labs-code/design.md) and lint-validated after generation |
| [`design-system-stylesheet`](./design-system-stylesheet/) | Renders an existing `DESIGN.md` as a self-contained `STYLE-SHEET.html` — a living document styled with the brand's own tokens |
| [`supplement-facts`](./supplement-facts/) | Converts a supplement label (raw HTML or an image) into accessible, responsive, FDA-structured HTML with one canonical stylesheet |
| [`commit-message`](./commit-message/) | Ship workflow: review changes, generate a Conventional Commits message, push, and open a PR |

Each skill directory has its own `README.md` with the full details.

---

## Workflow

The two design system skills are designed to be used in sequence:

```
   create-design-md              design-system-stylesheet
        │                                │
        ▼                                ▼
  User interview  ──→  DESIGN.md  ──→  STYLE-SHEET.html
  (6 steps)       (YAML + 10 sections,    (visual rendering)
                   lint-validated)
```

1. Run the **create-design-md** skill to interview the user and produce a `DESIGN.md` (validated against the Google DESIGN.md spec)
2. Run the **design-system-stylesheet** skill to visualize that `DESIGN.md` as an interactive HTML page

---

## Skill Format

Each skill is a directory containing a `SKILL.md` file with YAML frontmatter:

```yaml
---
name: skill-name
description: Trigger phrases and description of when the skill should activate
---
```

The Markdown body defines the skill's workflow, rules, and generation guidelines. Skills may include a `references/` directory with supplementary data files, an `assets/` directory with templates or stylesheets, and a `README.md` documenting the skill for humans.
