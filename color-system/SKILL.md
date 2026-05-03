---
name: color-system
description: >
  Generate a production-ready color token system for any digital product design system.
  Use this skill whenever a user wants to create color tokens, build or refactor a design
  system's color layer, define a palette for a new product, align UI colors with brand
  identity, or prepare Figma variables for engineering handoff. Trigger even if the user
  only mentions "design tokens", "color palette", "brand colors", "dark mode", "Figma
  variables", or "token system" — this skill covers the full primitive → semantic →
  component pipeline and handles single-brand, multi-brand, and accent-only setups.
---

# Color System Skill

Build a complete, accessible, mode-aware color token system for any product configuration.
Architecture: **Primitive → Semantic → Component**, implemented in Figma Variables.

This skill is interactive. Always interview the designer first, then generate.

---

## Phase 1 — Interview

Before producing anything, ask these questions in a single message.
Do not split into multiple rounds. Do not assume answers.

```
1. Brand colors
   - How many brand colors does your product have? (1, 2, or 3+)
   - Share the HEX values. If multiple, which is the primary?

2. Accent / additional colors
   - Do you have accent colors separate from the brand? (yes / no)
   - If yes, share the HEX values.

3. Dark mode
   - Is dark mode required, optional, or not needed?

4. Product type
   - What kind of product is this?
     SaaS / mobile app / dashboard / marketing site / e-commerce / other

5. Existing system
   - Are you starting from scratch, or is there an existing color system?
     none / partial (some tokens exist) / mature (full system, needs refactor)

6. Accessibility target
   - WCAG AA (standard) or AAA (strict)?

7. Engineering handoff format
   - How will tokens reach the dev team?
     Figma Variables only / CSS custom properties / JSON (W3C tokens) / Style Dictionary
```

Wait for answers. Then proceed to Phase 2.

---

## Phase 2 — Route Based on Brand Configuration

Use answers to determine the correct setup path.

### Decision tree

```
How many brand colors?
│
├── 1 brand color
│   └── Has accent? → single-brand-with-accent
│   └── No accent?  → single-brand
│
├── 2 brand colors
│   └── Has accent? → dual-brand-with-accent
│   └── No accent?  → dual-brand
│
└── 3+ brand colors → multi-brand
```

**Then read the matching reference file:**

| Configuration         | Reference file                              |
|-----------------------|---------------------------------------------|
| single-brand          | `references/single-brand.md`               |
| single-brand-accent   | `references/single-brand.md` (accent section) |
| dual-brand            | `references/multi-brand.md`                |
| dual-brand-accent     | `references/multi-brand.md` (accent section)  |
| multi-brand           | `references/multi-brand.md`                |

Read the reference file now. Follow its instructions for token structure.

---

## Phase 3 — Generate (follows reference file instructions)

The reference file defines exact steps. Summary of what every path produces:

### Step A — Primitive scales
Generate tonal scales (50–950, 11 steps) using OKLCH for perceptual uniformity.
→ Details in reference file.

### Step B — Semantic tokens
Map primitives to intent-based names. Cover: background, surface, border, text,
brand roles, feedback (error/success/warning/info), interaction states.
→ Details in reference file.

### Step C — Component tokens
Alias from semantic layer only. Cover: button variants, input, card, badge, nav item.
→ Details in reference file.

### Step D — Accessibility audit
Check required contrast pairs. Flag failures with fix suggestions.
→ Pairs listed in reference file.

### Step E — Design rationale
Short explanation of key decisions and trade-offs.

---

## Phase 4 — Figma Implementation

After generating tokens, walk the designer through Figma setup step by step.

### Step 1 — Create Variable collections

In Figma: **Assets panel → Local variables → + (New collection)**

Create three collections in this order:

| Collection name | Purpose |
|-----------------|---------|
| `primitive`     | Raw HEX values, no aliases, no modes |
| `semantic`      | Aliases pointing to primitive; has Light + Dark modes |
| `component`     | Aliases pointing to semantic only; inherits modes |

### Step 2 — Populate `primitive` collection

- Set collection type to **Color**
- No modes needed — single value per variable
- Create one group per scale: `brand-primary`, `gray`, `red`, `green`, `yellow`, `blue`
- Name each variable: `brand-primary/500`, `gray/100`, etc.
- Enter the HEX value directly

### Step 3 — Populate `semantic` collection

- Add two modes: **Light** and **Dark** (click "+" next to Modes)
- Create variables using slash naming: `background/default`, `text/subtle`, etc.
- For each variable: click the value field → choose "Create alias" → point to the matching primitive variable
- Set Light value and Dark value separately for each token

### Step 4 — Populate `component` collection

- No new modes needed — this collection inherits from semantic
- Create variables: `button-primary/background/default`, etc.
- Every value must be an alias to a `semantic` variable, never to `primitive` directly

### Step 5 — Apply variables in designs

- Select a layer → Fill/Stroke → click the style icon → switch to Variables tab
- Choose the component or semantic token
- To switch between Light/Dark: select the frame → right-click → "Change variable mode"

### Step 6 — Export for engineering

Depending on `handoff_format`:

| Format | Method |
|--------|--------|
| Figma Variables only | Share file with dev, they use Figma Dev Mode |
| CSS custom properties | Use **Tokens Studio** plugin → Export → CSS |
| JSON (W3C) | Use **Tokens Studio** → Export → JSON |
| Style Dictionary | Use **Tokens Studio** → Export → Style Dictionary |

**Tokens Studio setup (if needed):**
1. Install plugin: Figma Community → "Tokens Studio for Figma"
2. Connect to GitHub/GitLab repository or export locally
3. Map your Figma Variable collections to token sets in Tokens Studio
4. Export in chosen format

---

## Phase 5 — Validation Checklist

Before handing off, confirm all of these with the designer:

- [ ] Every semantic token has both a Light and Dark value
- [ ] No component token points directly to a primitive
- [ ] `brand/on` tokens exist for every filled brand surface
- [ ] `feedback/{state}/on` tokens exist for all feedback fills
- [ ] Contrast audit passed (or failures are documented with accepted trade-offs)
- [ ] Figma Variables are scoped correctly (component tokens = "All scopes")
- [ ] Engineering handoff format is exported and confirmed with dev team

---

## Reference Files

| File | When to read |
|------|--------------|
| `references/single-brand.md` | 1 brand color, with or without accent |
| `references/multi-brand.md`  | 2+ brand colors or complex accent structure |

`multi-brand.md` builds on top of `single-brand.md` — read both when routing to multi-brand.

---

## Constraints — always apply

- Never use pure `#000000` or `#FFFFFF` — use near-black/near-white from gray scale
- Component tokens must alias semantic, never primitive directly
- `/on` foreground tokens are mandatory for every filled brand or feedback surface
- If `dark_mode` is "optional": still define dark values, mark as "optional to implement"
- Always include resolved HEX values alongside token names in output tables
- If `existing_system` is partial or mature: audit first, propose migration path, do not replace blindly

---

## What NOT to Do

- Do not skip the interview — never assume brand count, dark mode need, or accent presence
- Do not generate tokens before reading the correct reference file
- Do not map component tokens to primitives — this breaks the propagation chain
- Do not skip `/on` tokens — button label color is the most common design system mistake
- Do not output only token names — always include resolved HEX values
- Do not use `visual_tone` input decoratively — it must affect chroma decisions in primitive scale
