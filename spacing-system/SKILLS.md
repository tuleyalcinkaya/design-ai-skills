---
name: spacing-system
description: >
  Generate a production-ready, scalable spacing, sizing, radius, and layout token system
  from product and platform inputs. Use this skill whenever a user wants to build or
  refactor a design system's spacing layer, create spacing tokens, define layout rhythm,
  align UI spacing across components, or prepare spacing-related tokens for engineering
  handoff. Trigger even if the user only mentions "spacing", "layout tokens", "Figma variables",
  "padding", "gap", "radius", "density", "grid", "layout", "component sizing", or
  "responsive spacing" — this skill covers the full primitive → semantic → component pipeline.
---

# Spacing System Skill

Build a complete, density-aware spacing token system following the
**Primitive → Semantic → Component** architecture. The output must be immediately
usable in Figma Variables and exportable for engineering.

Spacing, radius, and size tokens must use dedicated domain-based collections and must not
be mixed with color, typography, or other token domains.

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
- `responsive_strategy` — fixed / breakpoint-based (default) / fluid
- `handoff_format` — figma-variables (default) / css-tokens / json / style-dictionary

**Density affects scope:**
- `compact` → tighter layout, more micro spacing, useful for data-heavy UIs
- `comfortable` → balanced spacing, default for most products
- `spacious` → larger layout and section spacing, useful for marketing, onboarding, and consumer-facing flows

**Component complexity affects scope:**
- `low` → core components only, fewer component-level variants
- `medium` → standard product UI components
- `high` → extended component coverage, density variants, table-heavy and enterprise patterns

**If `existing_system` is `partial` or `mature`:**
→ Begin with a spacing audit. List existing spacing, radius, and size values, flag inconsistencies,
identify one-off values, and propose an incremental migration path rather than a full replacement.

---

## Step 1 — Generate Primitive Spacing Scale

Generate a numeric spacing scale based on the selected `base_unit`.

### Recommended default scale

For most digital products, use a **4px base unit** with an 8px visual rhythm.
Use micro spacing only when it has a clear purpose.

| Token | Value | Notes |
|-------|-------|-------|
| `primitive-spacing/0` | 0px | No spacing |
| `primitive-spacing/025` | 1px | Hairline / rare micro adjustment |
| `primitive-spacing/050` | 2px | Micro spacing |
| `primitive-spacing/100` | 4px | Base unit |
| `primitive-spacing/150` | 6px | Optional micro step |
| `primitive-spacing/200` | 8px | Small spacing |
| `primitive-spacing/300` | 12px | Small-medium spacing |
| `primitive-spacing/400` | 16px | Default component spacing |
| `primitive-spacing/500` | 20px | Medium spacing |
| `primitive-spacing/600` | 24px | Large component spacing |
| `primitive-spacing/700` | 32px | Section/content spacing |
| `primitive-spacing/800` | 40px | Large layout spacing |
| `primitive-spacing/900` | 48px | Page/section spacing |
| `primitive-spacing/1000` | 64px | Large section spacing |
| `primitive-spacing/1100` | 80px | Hero / spacious layout |
| `primitive-spacing/1200` | 96px | Extra-large layout spacing |

### Density rules

If `density` is `compact`:
- Prioritize `primitive-spacing/050`, `100`, `200`, `300`, `400`
- Use smaller component padding and gaps
- Useful for dashboards, tables, admin panels, and enterprise products
- Ensure interactive targets remain accessible

If `density` is `comfortable`:
- Use 4px base and 8px rhythm
- Default component padding should usually sit between 12px–24px
- Best default for most SaaS and mobile products

If `density` is `spacious`:
- Increase layout, page, and section spacing
- Use generous container and content gaps
- Keep component spacing readable but not oversized

### Output format — Primitive Spacing Scale

Output as a markdown table:

```
| Token                   | Value | Usage guidance |
|-------------------------|-------|----------------|
| primitive-spacing/100   | 4px   | Base unit |
| primitive-spacing/200   | 8px   | Small gap |
| primitive-spacing/400   | 16px  | Default component padding |
...
```

---

## Step 2 — Define Semantic Spacing Tokens

Map primitive values to **intent-based names**. Semantic spacing tokens must reference
primitive-spacing aliases, not raw pixel values.

### 2A — Layout tokens

```
semantic-spacing/layout/page-margin/sm
semantic-spacing/layout/page-margin/md
semantic-spacing/layout/page-margin/lg
semantic-spacing/layout/page-margin/xl

semantic-spacing/layout/container-gap/sm
semantic-spacing/layout/container-gap/md
semantic-spacing/layout/container-gap/lg

semantic-spacing/layout/section-gap/sm
semantic-spacing/layout/section-gap/md
semantic-spacing/layout/section-gap/lg
semantic-spacing/layout/section-gap/xl

semantic-spacing/layout/grid-gap/sm
semantic-spacing/layout/grid-gap/md
semantic-spacing/layout/grid-gap/lg
```

### 2B — Content tokens

```
semantic-spacing/content/group-gap/xs
semantic-spacing/content/group-gap/sm
semantic-spacing/content/group-gap/md
semantic-spacing/content/group-gap/lg

semantic-spacing/content/item-gap/xs
semantic-spacing/content/item-gap/sm
semantic-spacing/content/item-gap/md
semantic-spacing/content/item-gap/lg

semantic-spacing/content/inline-gap/xs
semantic-spacing/content/inline-gap/sm
semantic-spacing/content/inline-gap/md
```

### 2C — Component spacing tokens

```
semantic-spacing/component/padding/xs
semantic-spacing/component/padding/sm
semantic-spacing/component/padding/md
semantic-spacing/component/padding/lg
semantic-spacing/component/padding/xl

semantic-spacing/component/gap/xs
semantic-spacing/component/gap/sm
semantic-spacing/component/gap/md
semantic-spacing/component/gap/lg
```

### 2D — Form spacing tokens

```
semantic-spacing/form/field-gap
semantic-spacing/form/label-gap
semantic-spacing/form/helper-gap
semantic-spacing/form/group-gap
semantic-spacing/form/section-gap
```

### 2E — Navigation spacing tokens

```
semantic-spacing/navigation/item-gap
semantic-spacing/navigation/section-gap
semantic-spacing/navigation/sidebar-padding
semantic-spacing/navigation/topbar-padding
```

### Output format — Semantic Spacing Tokens

Output as a table with primitive alias and resolved value:

```
| Token                                      | Primitive alias              | Value |
|--------------------------------------------|------------------------------|-------|
| semantic-spacing/component/padding/md      | primitive-spacing/400        | 16px  |
| semantic-spacing/content/item-gap/sm       | primitive-spacing/200        | 8px   |
...
```

---

## Step 3 — Component Spacing Token System

Component spacing tokens alias from semantic-spacing tokens only — **never directly from primitives**.
This ensures spacing decisions can be updated at the semantic layer and propagate across components.

### Naming convention

```
component-spacing/{component}/{property}/{variant-or-state}
```

Properties:
- `padding-x`
- `padding-y`
- `padding`
- `gap`
- `margin`
- `inset`
- `stack-gap`
- `section-gap`
- `cell-padding-x`
- `cell-padding-y`

Variants:
- `xs`, `sm`, `md`, `lg`, `xl`
- `compact`, `comfortable`, `spacious`
- `default`, `dense`, `expanded`

### Component coverage

Do not limit component tokens to a fixed component list. Generate component token patterns for all relevant components in the product.

At minimum, support patterns for:

- Button
- Input
- Select
- Checkbox / Radio / Switch
- Card / Container
- Modal / Dialog
- Drawer / Sidebar
- Table
- List
- Navigation item
- Tabs
- Badge / Tag
- Toast / Notification
- Tooltip / Popover
- Empty state
- Form group
- Page layout

### Example mappings

**Button**

```
component-spacing/button/padding-x/sm       → semantic-spacing/component/padding/sm
component-spacing/button/padding-y/sm       → semantic-spacing/component/padding/xs
component-spacing/button/gap/default        → semantic-spacing/component/gap/sm
```

**Input**

```
component-spacing/input/padding-x/default   → semantic-spacing/component/padding/md
component-spacing/input/padding-y/default   → semantic-spacing/component/padding/sm
component-spacing/input/helper-gap          → semantic-spacing/form/helper-gap
```

**Card**

```
component-spacing/card/padding/default      → semantic-spacing/component/padding/lg
component-spacing/card/gap/default          → semantic-spacing/content/group-gap/md
```

**Table**

```
component-spacing/table/cell-padding-x/compact  → semantic-spacing/component/padding/sm
component-spacing/table/cell-padding-y/compact  → semantic-spacing/component/padding/xs
component-spacing/table/cell-padding-x/default  → semantic-spacing/component/padding/md
component-spacing/table/cell-padding-y/default  → semantic-spacing/component/padding/sm
```

### Output format — Component Spacing Tokens

```
### Button

| Token                                      | Value (semantic alias)                  |
|--------------------------------------------|-----------------------------------------|
| component-spacing/button/padding-x/sm      | semantic-spacing/component/padding/sm   |
| component-spacing/button/padding-y/sm      | semantic-spacing/component/padding/xs   |
...
```

---

## Step 4 — Radius Tokens

Create radius tokens as a separate domain. Do not place radius tokens inside spacing collections.

### Primitive radius scale

```
primitive-radius/none   → 0px
primitive-radius/xs     → 2px
primitive-radius/sm     → 4px
primitive-radius/md     → 8px
primitive-radius/lg     → 12px
primitive-radius/xl     → 16px
primitive-radius/2xl    → 24px
primitive-radius/full   → 9999px
```

### Semantic radius tokens

```
semantic-radius/component/small
semantic-radius/component/default
semantic-radius/component/large
semantic-radius/container/default
semantic-radius/container/large
semantic-radius/interactive/default
semantic-radius/pill
```

### Component radius tokens

Component radius tokens alias from semantic-radius tokens only.

```
component-radius/button/default       → semantic-radius/interactive/default
component-radius/input/default        → semantic-radius/interactive/default
component-radius/card/default         → semantic-radius/container/default
component-radius/modal/default        → semantic-radius/container/large
component-radius/badge/default        → semantic-radius/pill
```

### Output format — Radius Tokens

```
| Token                              | Alias / Value |
|------------------------------------|---------------|
| primitive-radius/md                | 8px |
| semantic-radius/component/default  | primitive-radius/md |
| component-radius/button/default    | semantic-radius/interactive/default |
```

---

## Step 5 — Size Tokens

Create sizing tokens for controls, icons, avatars, hit areas, and common UI elements.
Do not place size tokens inside spacing collections.

### Primitive size scale

```
primitive-size/12  → 12px
primitive-size/16  → 16px
primitive-size/20  → 20px
primitive-size/24  → 24px
primitive-size/32  → 32px
primitive-size/40  → 40px
primitive-size/44  → 44px
primitive-size/48  → 48px
primitive-size/56  → 56px
primitive-size/64  → 64px
```

### Semantic control sizes

```
semantic-size/control/xs
semantic-size/control/sm
semantic-size/control/md
semantic-size/control/lg
semantic-size/control/xl
```

Recommended defaults:
- xs → `primitive-size/24`
- sm → `primitive-size/32`
- md → `primitive-size/40`
- lg → `primitive-size/48`
- xl → `primitive-size/56`

### Semantic icon sizes

```
semantic-size/icon/xs
semantic-size/icon/sm
semantic-size/icon/md
semantic-size/icon/lg
semantic-size/icon/xl
```

Recommended defaults:
- xs → `primitive-size/12`
- sm → `primitive-size/16`
- md → `primitive-size/20`
- lg → `primitive-size/24`
- xl → `primitive-size/32`

### Semantic avatar sizes

```
semantic-size/avatar/sm
semantic-size/avatar/md
semantic-size/avatar/lg
semantic-size/avatar/xl
```

### Accessibility hit-area tokens

```
semantic-size/hit-area/minimum-touch
semantic-size/hit-area/minimum-desktop
```

Recommended defaults:
- minimum-touch → `primitive-size/44`
- minimum-desktop → `primitive-size/32`

### Component size tokens

Component size tokens alias from semantic-size tokens only.

```
component-size/button/height/sm       → semantic-size/control/sm
component-size/button/height/md       → semantic-size/control/md
component-size/input/height/default   → semantic-size/control/md
component-size/icon/default           → semantic-size/icon/md
component-size/avatar/default         → semantic-size/avatar/md
```

---

## Step 6 — Layout Guidance

Define layout recommendations based on `product_type`, `platform`, `density`, and `responsive_strategy`.

Include:

1. Page margins
2. Container max-widths
3. Grid gutters
4. Section spacing
5. Card spacing
6. Responsive spacing behavior

### Responsive tokens

If `platform` includes responsive web, define breakpoint-aware semantic tokens:

```
semantic-spacing/layout/page-margin/mobile
semantic-spacing/layout/page-margin/tablet
semantic-spacing/layout/page-margin/desktop
semantic-spacing/layout/page-margin/wide

semantic-spacing/layout/grid-gap/mobile
semantic-spacing/layout/grid-gap/tablet
semantic-spacing/layout/grid-gap/desktop
```

Do not simply scale all spacing down on mobile. Prioritize:
- readability
- touch comfort
- hierarchy
- content density

---

## Step 7 — Accessibility & Usability Audit

Check the spacing system against usability expectations.

Audit:

- Are touch targets large enough for the selected platform?
- Are dense layouts still readable?
- Are related items visually grouped?
- Are unrelated sections sufficiently separated?
- Are form labels, helper texts, and error messages visually connected to their fields?
- Are table cell paddings appropriate for the selected density?
- Are navigation items easy to scan and interact with?
- Are radius and sizing decisions consistent with the selected density and product tone?

Output format:

```
| Check                                      | Status | Recommendation |
|--------------------------------------------|--------|----------------|
| Touch target minimum on mobile             | ✅     | 44px minimum defined |
| Form helper text proximity                 | ✅     | helper-gap uses compact semantic token |
| Table density                              | ⚠️     | compact table needs separate cell padding |
```

---

## Step 8 — Figma Implementation

### Variable collections

Spacing, radius, and size tokens must be stored in dedicated domain-based collections.
Do not mix them with color, typography, motion, or other token domains.

| Collection              | Scope      | Contains |
|-------------------------|------------|----------|
| `primitive-spacing`     | Local only | Raw spacing values |
| `semantic-spacing`      | All scopes | Aliases → primitive-spacing |
| `component-spacing`     | All scopes | Aliases → semantic-spacing only |
| `primitive-radius`      | Local only | Raw radius values |
| `semantic-radius`       | All scopes | Aliases → primitive-radius |
| `component-radius`      | All scopes | Aliases → semantic-radius only |
| `primitive-size`        | Local only | Raw size values |
| `semantic-size`         | All scopes | Aliases → primitive-size |
| `component-size`        | All scopes | Aliases → semantic-size only |

Other token types must use separate collections:
- Color tokens → `primitive-color`, `semantic-color`, `component-color`
- Typography tokens → dedicated typography collections
- Motion tokens → dedicated motion collections

### Mode setup

- Spacing, radius, and size collections usually use a single mode.
- If the product requires density modes, add modes to semantic and component collections:
  - Compact
  - Comfortable
  - Spacious
- Primitive collections should usually remain single-mode raw values.
- Component collections should inherit from semantic collections via aliasing.

### Variable naming

Slash notation, exactly as specified:
- `primitive-spacing/400`
- `semantic-spacing/component/padding/md`
- `component-spacing/button/padding-x/sm`
- `primitive-radius/md`
- `semantic-radius/component/default`
- `component-radius/button/default`
- `primitive-size/40`
- `semantic-size/control/md`
- `component-size/button/height/md`

### Figma documentation page

Create a **"📐 Spacing System"** page with sections:

1. **Primitive Spacing Scale** — token, px value, usage
2. **Semantic Spacing Tokens** — grouped by layout/content/component/form/navigation
3. **Component Spacing Tokens** — grouped by component, alias chain: component → semantic → primitive
4. **Radius Tokens** — primitive, semantic, and component radius mapping
5. **Size Tokens** — primitive, semantic, and component size mapping
6. **Responsive Layout Rules** — page margins, gutters, breakpoints
7. **Density Modes** — compact / comfortable / spacious, if applicable
8. **Usage Guidelines** — when to use semantic vs component tokens; anti-patterns

---

## Step 9 — Engineering Handoff

Based on `handoff_format` input:

**`css-tokens`**

```css
:root {
  --primitive-spacing-400: 16px;
  --semantic-spacing-component-padding-md: var(--primitive-spacing-400);
  --component-spacing-button-padding-x-sm: var(--semantic-spacing-component-padding-sm);

  --primitive-radius-md: 8px;
  --semantic-radius-component-default: var(--primitive-radius-md);

  --primitive-size-40: 40px;
  --semantic-size-control-md: var(--primitive-size-40);
}
```

**`json`** → W3C Design Token format (`.value`, `.type`, `$description`)

**`style-dictionary`** → `tokens/spacing.json` + `tokens/radius.json` + `tokens/size.json`

Only output the handoff format that was requested. Default to `figma-variables` if unspecified.

---

## What NOT to Do

- Do not place spacing tokens inside color token collections
- Do not create arbitrary one-off spacing values without system logic
- Do not map component tokens directly to raw pixel values
- Do not ignore product density
- Do not use only 8px spacing if the product needs micro spacing
- Do not treat layout spacing and component padding as the same thing
- Do not skip responsive spacing guidance for responsive products
- Do not define radius and size inconsistently with spacing rhythm
- Do not ignore accessibility and touch target requirements
- Do not mix domain and abstraction naming, such as `primitive/spacing/400`; use `primitive-spacing/400`

---

## Example Invocations

**Compact SaaS dashboard:**
```
/spacing-system
product_type: SaaS dashboard
platform: responsive web
density: compact
existing_system: none
base_unit: 4px
target_screens: desktop, tablet
accessibility_level: strict
```

**Comfortable mobile app:**
```
/spacing-system
product_type: mobile app
platform: iOS and Android
density: comfortable
existing_system: partial
base_unit: 4px
target_screens: mobile
accessibility_level: strict
handoff_format: figma-variables
```

**Spacious marketing site:**
```
/spacing-system
product_type: marketing
platform: responsive web
density: spacious
existing_system: none
base_unit: 8px
target_screens: mobile, desktop, wide
handoff_format: css-tokens
```
