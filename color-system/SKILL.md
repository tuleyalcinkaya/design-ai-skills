---
name: color-system
description: >
  Generate a production-ready, scalable color token system from brand inputs.
  Use this skill whenever a user wants to build or refactor a design system's color layer,
  create color tokens for a new product, align UI colors with a brand identity, or prepare
  color tokens for engineering handoff. Trigger even if the user only mentions "design tokens",
  "color palette", "brand colors", "dark mode colors", or "Figma variables" — this skill covers
  the full primitive → semantic → component pipeline.
---

# Color System Skill

Build a complete, accessible, mode-aware color token system following the
**Primitive → Semantic → Component** architecture. The output must be immediately
usable in Figma Variables and exportable for engineering.

---

## Step 0 — Gather Inputs

Before generating anything, confirm all required inputs. If any are missing, ask for them.

**Required:**
- `brand_colors` — 1–3 HEX values. If multiple, ask which is primary.
- `product_type` — SaaS / mobile app / dashboard / marketing / e-commerce / other
- `dark_mode` — required / optional / none
- `existing_system` — none / partial / mature

**Optional (use defaults if not provided):**
- `accent_colors` — additional HEX values beyond brand palette
- `visual_tone` — minimal (default) / bold / playful / corporate
- `color_density` — low / medium (default) / high
- `accessibility_level` — AA (default) / AAA
- `handoff_format` — figma-variables (default) / css-tokens / json / style-dictionary

**Color density affects scope:**
- `low` → 7-step scales (50, 100, 200, 400, 600, 800, 950), fewer semantic variants
- `medium` → 11-step scales (50–950), standard semantic set
- `high` → 11-step scales + alpha variants + extended semantic set

**If `existing_system` is `partial` or `mature`:**
→ Begin with a token audit. List existing tokens, flag gaps and inconsistencies,
then propose an incremental migration path rather than a full replacement.

---

## Step 1 — Generate Primitive Scales

Generate tonal scales using **OKLCH** color model for perceptual uniformity.
Each step should have a consistent lightness progression so contrast ratios are predictable.

### Required scales

| Scale | Source | Notes |
|-------|--------|-------|
| `brand-primary` | Input HEX | Place input color near step 500; adjust if needed for scale harmony |
| `brand-secondary` | Input HEX (if provided) | Same rule |
| `accent` | Input HEX (if provided) | Same rule |
| `gray` | Derived | Slightly cool-toned (hue 220–240°) unless brand is warm |
| `red` | System | For error states |
| `green` | System | For success states |
| `yellow` | System | For warning states — ensure sufficient contrast on white |
| `blue` | System | For info states |

### Scale steps

For `medium` density (default): **50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950**

For each step, output:
- Step number
- HEX value
- OKLCH values (L, C, H)
- Contrast ratio vs white (`#FFFFFF`)
- Contrast ratio vs near-black (`gray-950`)

**Constraints:**
- Step 50 must be near-white, step 950 near-black — never pure `#000` or `#FFF`
- Steps 400–600 are "mid-range" and must contain the brand input color
- Lightness must decrease monotonically from 50 → 950
- Chroma should peak around 400–500 and taper at extremes

### Alpha variants (required for `high` density, optional for `medium`)

For `gray` and `brand-primary`, add alpha scale:
`/a10, /a20, /a30, /a40, /a50, /a60, /a70, /a80, /a90`

These are used for overlays, scrims, and shadows.

### Output format — Primitive Scales

Output as a markdown table per color:

```
### brand-primary

| Step | HEX     | OKLCH               | vs white | vs dark |
|------|---------|---------------------|----------|---------|
| 50   | #FFF0F8 | oklch(97% 0.02 0)   | 19.1:1   | 1.1:1   |
| 100  | ...     | ...                 | ...      | ...     |
...
```

---

## Step 2 — Define Semantic Tokens

Map primitive values to **intent-based names**. Semantic tokens must never reference
step numbers in their value — always reference another semantic token or a primitive alias.

Provide values for **both light and dark** modes in every token table.

### 2A — Foundation tokens

```
semantic/background/default          → Light: gray-50    Dark: gray-950
semantic/background/subtle           → Light: gray-100   Dark: gray-900
semantic/background/inverse          → Light: gray-950   Dark: gray-50

semantic/surface/default             → Light: white      Dark: gray-900
semantic/surface/raised              → Light: white      Dark: gray-800
semantic/surface/overlay             → Light: white      Dark: gray-800
semantic/surface/scrim               → Light: gray/a40   Dark: gray/a60

semantic/border/default              → Light: gray-200   Dark: gray-700
semantic/border/subtle               → Light: gray-100   Dark: gray-800
semantic/border/strong               → Light: gray-400   Dark: gray-500
semantic/border/focus                → Light: brand-primary-500  Dark: brand-primary-400
```

### 2B — Text tokens

```
semantic/text/default                → Light: gray-900   Dark: gray-50
semantic/text/subtle                 → Light: gray-600   Dark: gray-400
semantic/text/disabled               → Light: gray-400   Dark: gray-600
semantic/text/inverse                → Light: gray-50    Dark: gray-900
semantic/text/on-brand               → Light: white      Dark: white (verify contrast)
semantic/text/link                   → Light: brand-primary-600  Dark: brand-primary-300
semantic/text/link-hover             → Light: brand-primary-700  Dark: brand-primary-200
```

### 2C — Brand tokens

For each brand color defined (primary / secondary / accent):

```
semantic/brand-primary/default       → brand-primary-500
semantic/brand-primary/hover         → brand-primary-600
semantic/brand-primary/active        → brand-primary-700
semantic/brand-primary/subtle        → brand-primary-100 (light) / brand-primary-900 (dark)
semantic/brand-primary/on            → white or gray-950 (whichever passes AA vs /default)
```

Repeat pattern for `brand-secondary` and `accent` if defined.
`/on` tokens are **foreground colors** to use on top of the brand fill — do not skip these.

### 2D — Feedback tokens

For each state: `error`, `success`, `warning`, `info`

```
semantic/feedback/{state}/default    → {color}-500 (light) / {color}-400 (dark)
semantic/feedback/{state}/subtle     → {color}-100 (light) / {color}-950 (dark)
semantic/feedback/{state}/border     → {color}-400 (light) / {color}-600 (dark)
semantic/feedback/{state}/text       → {color}-700 (light) / {color}-300 (dark)
semantic/feedback/{state}/on         → white or near-black (verify contrast vs /default)
```

### 2E — Interaction tokens

```
semantic/state/hover                 → gray/a08 (light) / gray/a10 (dark)
semantic/state/active                → gray/a12 (light) / gray/a16 (dark)
semantic/state/selected              → brand-primary-100 (light) / brand-primary-900 (dark)
semantic/state/focus-ring            → brand-primary-500 (same in both modes)
semantic/state/disabled-fill         → gray-100 (light) / gray-800 (dark)
semantic/state/disabled-text         → gray-400 (light) / gray-600 (dark)
```

### Output format — Semantic Tokens

Output as a single table with light + dark columns:

```
| Token                              | Light value        | Dark value         |
|------------------------------------|--------------------|--------------------|
| semantic/background/default        | gray-50 (#F9FAFB)  | gray-950 (#0A0A0F) |
| semantic/text/default              | gray-900 (#111827) | gray-50 (#F9FAFB)  |
...
```

Include actual HEX values in parentheses for every entry.

---

## Step 3 — Component Token System

Component tokens alias from semantic tokens only — **never directly from primitives.**
This ensures a single semantic change propagates everywhere.

### Naming convention

```
component/{component}/{property}/{state}
```

States: `default`, `hover`, `active`, `focus`, `disabled`, `error`, `selected`
Properties: `background`, `border`, `text`, `icon`, `shadow`, `outline`

### Required components

Define the following at minimum:

**Button (primary, secondary, ghost, destructive variants)**
```
component/button-primary/background/default   → semantic/brand-primary/default
component/button-primary/background/hover     → semantic/brand-primary/hover
component/button-primary/background/active    → semantic/brand-primary/active
component/button-primary/background/disabled  → semantic/state/disabled-fill
component/button-primary/text/default         → semantic/brand-primary/on
component/button-primary/text/disabled        → semantic/state/disabled-text
component/button-primary/border/focus         → semantic/state/focus-ring
```

Repeat pattern for `button-secondary`, `button-ghost`, `button-destructive`.

**Input**
```
component/input/background/default    → semantic/surface/default
component/input/border/default        → semantic/border/default
component/input/border/hover          → semantic/border/strong
component/input/border/focus          → semantic/border/focus
component/input/border/error          → semantic/feedback/error/border
component/input/text/default          → semantic/text/default
component/input/text/placeholder      → semantic/text/subtle
component/input/text/disabled         → semantic/state/disabled-text
component/input/background/disabled   → semantic/state/disabled-fill
```

**Card / Container**
```
component/card/background/default     → semantic/surface/default
component/card/background/raised      → semantic/surface/raised
component/card/border/default         → semantic/border/subtle
```

**Badge / Tag**
```
component/badge-{variant}/background  → semantic/feedback/{state}/subtle
component/badge-{variant}/text        → semantic/feedback/{state}/text
component/badge-{variant}/border      → semantic/feedback/{state}/border
```

**Navigation item**
```
component/nav-item/background/default   → transparent
component/nav-item/background/hover     → semantic/state/hover
component/nav-item/background/selected  → semantic/state/selected
component/nav-item/text/default         → semantic/text/subtle
component/nav-item/text/selected        → semantic/brand-primary/default
```

### Shadow tokens

Shadows use alpha primitives directly (Figma limitation — variables can't reference alpha in shadow fields).

```
component/shadow/sm   → gray/a08 — offset: 0 1px 2px
component/shadow/md   → gray/a12 — offset: 0 4px 6px
component/shadow/lg   → gray/a16 — offset: 0 10px 15px
```

In dark mode, increase alpha by ~1.5× for visibility.

### Output format — Component Tokens

Output grouped by component, showing the semantic alias (not the resolved HEX):

```
### Button — Primary

| Token                                        | Value (semantic alias)              |
|----------------------------------------------|-------------------------------------|
| component/button-primary/background/default  | semantic/brand-primary/default      |
| component/button-primary/background/hover    | semantic/brand-primary/hover        |
...
```

---

## Step 4 — Accessibility Audit

Run contrast checks on the following pairs and flag any failure as ⚠️ with a fix suggestion.

**Text on backgrounds:**
- `text/default` on `background/default` → ≥ 4.5:1
- `text/subtle` on `background/default` → ≥ 4.5:1
- `text/disabled` on `background/default` → ≥ 3:1
- `text/on-brand` on `brand-primary/default` → ≥ 4.5:1
- `feedback/{state}/text` on `feedback/{state}/subtle` → ≥ 4.5:1 per state

**Interactive UI:**
- `button-primary/background/default` vs surrounding surface → ≥ 3:1
- `input/border/default` vs `surface/default` → ≥ 3:1
- `state/focus-ring` vs any adjacent surface → ≥ 3:1

Repeat all checks for dark mode values.

If `accessibility_level` is `AAA`, raise text threshold to 7:1.

**Output format:**

```
| Pair                                      | Light ratio | Dark ratio | Status  |
|-------------------------------------------|-------------|------------|---------|
| text/default on background/default        | 16.2:1      | 15.8:1     | ✅ AA   |
| text/on-brand on brand-primary/default    | 3.8:1       | 4.1:1      | ⚠️ Fail |
```

---

## Step 5 — Design Rationale

Write a concise rationale covering:

1. **Brand color placement** — which step the input HEX landed on, any adjustments made
2. **Gray undertone** — why warm/cool/neutral was chosen based on brand hue
3. **Feedback color choices** — especially if any conflict with brand color (e.g. brand is red → error needs differentiation)
4. **Dark mode strategy** — contrast preservation, any brand color shifts for dark backgrounds
5. **Trade-offs** — any decisions where accessibility and aesthetics conflicted

---

## Step 6 — Figma Implementation

### Variable collections

Color tokens must be stored in dedicated collections.
Do not mix color tokens with spacing, typography, or other token types.

| Collection             | Scope      | Contains |
|------------------------|------------|----------|
| `primitive-color`      | Local only | All tonal scale values as raw HEX |
| `semantic-color`       | All scopes | Aliases → primitive-color, with Light + Dark modes |
| `component-color`      | All scopes | Aliases → semantic-color only |

Other token types (spacing, typography, radius, etc.) must use separate collections.

### Mode setup

- In `semantic` collection: add two modes — **Light** and **Dark**
- Every token must have a value in both modes
- In `primitive` collection: single mode only (raw values, no theming)
- `component` collection inherits mode from `semantic` via aliasing — no separate modes needed

### Variable naming

Slash notation, exactly as specified:
- `primitive-color/brand-primary/500`
- `semantic-color/background/default`
- `component-color/button-primary/background/hover`

### Figma documentation page

Create a **"🎨 Color System"** page with sections:

1. **Primitive Scales** — Color chip grid per scale, HEX + step label
2. **Semantic Tokens** — Two-column table (Light | Dark), chip + name + HEX
3. **Component Tokens** — Grouped by component, alias chain: component → semantic → primitive
4. **Contrast Matrix** — Text/background pairs with ratio labels, pass/fail status
5. **Usage Guidelines** — When to use semantic vs brand tokens; anti-patterns

---

## Step 7 — Engineering Handoff

Based on `handoff_format` input:

**`css-tokens`**
```css
:root {
  --color-background-default: #F9FAFB;
  --color-text-default: #111827;
  ...
}
[data-theme="dark"] {
  --color-background-default: #0A0A0F;
  --color-text-default: #F9FAFB;
  ...
}
```

**`json`** → W3C Design Token format (`.value`, `.type`, `$extensions.mode`)

**`style-dictionary`** → `tokens/primitive.json` + `tokens/semantic.json`, ready for Style Dictionary v3

Only output the handoff format that was requested. Default to `figma-variables` if unspecified.

---

## What NOT to Do

- Do not map component tokens directly to primitives — always go through semantic
- Do not skip `/on` foreground tokens for brand and feedback fills
- Do not assume single brand color — always check `brand_colors` input
- Do not skip dark mode values if `dark_mode` is `optional` — define them, mark as optional to implement
- Do not apply `color_density` input without adjusting step count AND semantic variant count
- Do not output token names without resolved HEX values — always include both
- Do not use pure `#000000` or `#FFFFFF` anywhere in the system
- Never mix domains inside the same collection.

---

## Example Invocations

**Minimal (single brand):**
```
/color-system
brand_colors: #E1007E
product_type: SaaS
dark_mode: required
existing_system: none
```

**Full (multi-brand, strict):**
```
/color-system
brand_colors: #0055FF (primary), #FF6B00 (secondary)
accent_colors: #00C2A8
product_type: dashboard
visual_tone: minimal
color_density: high
dark_mode: required
accessibility_level: AAA
handoff_format: css-tokens
existing_system: partial
```
