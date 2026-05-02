---
name: spacing-system
description: >
  Generate a production-ready, scalable spacing, sizing, radius, and layout token system
  from product and platform inputs. Use this skill whenever a user wants to build or
  refactor a design system's spacing layer, create spacing tokens, define layout rhythm,
  align UI spacing across components, or prepare spacing-related tokens for engineering
  handoff. Trigger even if the user only mentions "spacing", "layout tokens", "Figma variables",
  "padding", "gap", "radius", "density", "grid", or "component sizing" — this skill covers
  the full primitive → semantic → component pipeline.
---

# Spacing System Skill

Build a complete, density-aware spacing token system following the
**Primitive → Semantic → Component** architecture. The output must be immediately
usable in Figma Variables and exportable for engineering.

---

## Step 0 — Gather Inputs

Before generating anything, confirm all required inputs. If any are missing, ask for them.

**Required:**
- `product_type` — SaaS / mobile app / dashboard / marketing / e-commerce / enterprise / other
- `platform` — web / responsive web / iOS / Android / cross-platform
- `density` — compact / comfortable / spacious
- `existing_system` — none / partial / mature

**Optional (use defaults if not provided):**
- `base_unit` — 4px (default) / 8px / custom
- `target_screens` — mobile / tablet / desktop / large dashboard
- `accessibility_level` — standard (default) / strict
- `component_complexity` — low / medium (default) / high
- `handoff_format` — figma-variables (default) / css-tokens / json / style-dictionary

---

## Step 1 — Generate Primitive Spacing Scale

Generate a numeric spacing scale based on the selected `base_unit`.

### Recommended default scale

| Token | Value | Notes |
|-------|-------|-------|
| `primitive/spacing/0` | 0px | No spacing |
| `primitive/spacing/050` | 2px | Micro spacing |
| `primitive/spacing/100` | 4px | Base unit |
| `primitive/spacing/200` | 8px | Small spacing |
| `primitive/spacing/300` | 12px | Small-medium spacing |
| `primitive/spacing/400` | 16px | Default component spacing |
| `primitive/spacing/500` | 20px | Medium spacing |
| `primitive/spacing/600` | 24px | Large component spacing |
| `primitive/spacing/700` | 32px | Section/content spacing |
| `primitive/spacing/800` | 40px | Large layout spacing |
| `primitive/spacing/900` | 48px | Page/section spacing |
| `primitive/spacing/1000` | 64px | Large section spacing |

---

## Step 2 — Define Semantic Spacing Tokens

Map primitive values to intent-based tokens.

### Layout
- `semantic/spacing/layout/page-margin`
- `semantic/spacing/layout/container-gap`
- `semantic/spacing/layout/section-gap`

### Content
- `semantic/spacing/content/group-gap`
- `semantic/spacing/content/item-gap`

### Component
- `semantic/spacing/component/padding`
- `semantic/spacing/component/gap`

---

## Step 3 — Component Token System

Component tokens must map from semantic tokens only.

Example:

`component/button/spacing/padding-x/sm → semantic/spacing/component/padding`

---

## Step 4 — Radius Tokens

- `primitive/radius/sm → 4px`
- `primitive/radius/md → 8px`
- `primitive/radius/lg → 12px`

---

## Step 5 — Size Tokens

- `semantic/size/control/md → 40px`
- `semantic/size/icon/md → 20px`

---

## Step 6 — Figma Implementation

Use collections:
- primitive
- semantic
- component

Naming:
- `primitive/spacing/400`
- `semantic/spacing/component/padding`
- `component/button/spacing/padding-x/sm`

---

## Example Usage

/spacing-system

product_type: SaaS
platform: web
density: compact
existing_system: none
