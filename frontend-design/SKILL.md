---
name: frontend-design
description: >
  This skill should be used when the user asks to "build a frontend", "create a UI",
  "design a page", "make it look good", "build a landing page", "create a dashboard",
  "style this", "beautify", "redesign", "improve the UI", "bikin halaman", "desain UI",
  "buat landing page", "buat komponen", or any task involving visual interface creation,
  component styling, layout design, responsive design, or frontend aesthetics. Also
  trigger when the user shows a screenshot or design reference and wants it implemented,
  or when building any user-facing web interface. This skill produces distinctive,
  production-grade interfaces that avoid generic AI aesthetics. Always use this skill
  for frontend work - even simple styling tasks benefit from its design system approach.
version: 1.0.0
---

# Frontend Design

Build distinctive, production-grade frontend interfaces. Not generic SaaS templates - real visual identities that look like a human designer made them.

The core philosophy: **design system first, components second, pages last.** Every color, shadow, gradient, spacing value, and animation lives in the design system. Components consume tokens. Pages compose components. No ad-hoc styling ever touches a component file.

---

## Core Workflow

### Step 1: Establish Visual Direction

Before writing any component code, define the visual identity. Ask or decide:

- **Mood**: What feeling should the interface evoke? (clinical precision, warm approachability, bold energy, quiet elegance)
- **Reference point**: What existing product or style does this resemble? (not to copy - to calibrate expectations)
- **Color strategy**: Monochromatic, complementary, analogous, or split-complementary?
- **Typography personality**: Geometric (modern/tech), humanist (friendly/approachable), or transitional (professional/neutral)?
- **Density**: Spacious/editorial or compact/data-dense?

If the user doesn't specify, make an opinionated choice based on the product type. Never default to "generic SaaS purple."

### Step 2: Build the Design System

Before any component exists, define all tokens. This is the single most important step. Everything flows from here.

**Mandatory token categories:**

1. **Colors** - Primary, secondary, accent, semantic (success/warning/error/info), neutrals scale, surface/background hierarchy
2. **Typography** - Font families, size scale (with line-heights), weight scale
3. **Spacing** - Consistent scale (4px base recommended: 4, 8, 12, 16, 24, 32, 48, 64, 96)
4. **Shadows** - Elevation levels (subtle, medium, prominent, dramatic)
5. **Radii** - Border radius scale (none, sm, md, lg, xl, full)
6. **Animations** - Timing functions, durations, named transitions
7. **Gradients** - Primary gradient, subtle backgrounds, accent gradients

All colors in HSL format. All tokens as CSS custom properties or framework-equivalent. See `references/design-system.md` for implementation patterns across frameworks.

### Step 3: Implement Components

Small, focused components. Each one:
- Consumes only design system tokens (never raw color values, never magic numbers)
- Has explicit variants for different use cases (not prop-soup)
- Handles all visual states: default, hover, focus, active, disabled, loading
- Works responsively without media-query overrides in consuming code

### Step 4: Compose Pages

Pages are compositions of components with layout. The page file should contain:
- Layout structure (grid/flex)
- Component placement
- Section spacing
- Responsive breakpoint adjustments

The page file should NOT contain:
- Color values
- Font sizes
- Shadow definitions
- Anything that belongs in the design system

For component patterns and layout strategies, consult `references/patterns.md`.

---

## Zero Visual Slop - Hard Rules

AI-generated frontends have a recognizable "smell." Users explicitly want output that is indistinguishable from work by a senior human designer. Every rule below exists because the default AI output violates it.

### The AI-SaaS-Starter-Kit Look (NEVER produce this)

- Purple-to-blue gradient hero with white text and a "Get Started" button
- Cards with rounded corners, subtle shadows, and identical padding
- Inter/system font at default weights throughout
- Gray-100 backgrounds with white cards
- Perfectly centered content with max-width 1200px on every section
- Generic stock-illustration-style hero images
- Feature grids of 3 identical cards with icon + title + paragraph
- Gradient text on headings for no design reason
- Dark mode that's just "invert the grays"

### What Expert Design Looks Like

- **Asymmetry with purpose** - Not everything centered. Visual tension creates interest.
- **Typographic hierarchy through contrast** - Massive heading next to small body text. Bold next to light. Not everything at comfortable middle sizes.
- **Color restraint** - One strong color used sparingly is more powerful than a full palette used everywhere. Most of the interface should be neutrals.
- **Whitespace as a design element** - Generous spacing between sections. Content breathes. Dense information only where density serves the use case (tables, dashboards).
- **Intentional imperfection** - Slight rotation, overlapping elements, broken grid. Perfectly aligned everything is the AI tell.
- **Typography that has personality** - Not just Inter. A serif headline with sans-serif body. A monospace accent. Font choice IS the design.
- **Depth through layering** - Overlapping elements, negative margins, z-index composition. Not flat cards on flat backgrounds.

### The Visual Slop Test

Before shipping any interface, check:

1. **Screenshot test**: Take a screenshot. Show it to someone without context. Can they tell it's AI-generated? If yes, redesign.
2. **Template test**: Does this look like it came from a template marketplace? If yes, add personality.
3. **Swap test**: Could you swap the logo/colors and use this for a completely different product? If yes, the design lacks identity.
4. **Squint test**: Squint at the screen. Is there clear visual hierarchy, or does everything blur into same-weight same-size sameness?

For the complete visual anti-slop guide with side-by-side examples, consult `references/anti-slop-visual.md`.

---

## Framework-Agnostic Principles

This skill works with any frontend framework. The design system implementation changes, the principles don't.

| Framework | Token Location | Component Variants |
|-----------|---------------|-------------------|
| React + Tailwind | `tailwind.config.ts` + `globals.css` | CVA / Tailwind Variants |
| React + CSS Modules | CSS custom properties in `:root` | className composition |
| Vue | CSS custom properties / Vuetify tokens | Props + slots |
| Svelte | CSS custom properties / Skeleton UI | Slot-based variants |
| Vanilla HTML/CSS | CSS custom properties in stylesheet | BEM with modifier classes |
| Next.js / Nuxt / SvelteKit | Same as underlying framework | Same as underlying framework |

Regardless of framework:
- **Never inline colors** - `className="text-white bg-blue-500"` is just as bad as `style={{color: 'white'}}`. Use semantic tokens: `className="text-primary-foreground bg-primary"`.
- **Never use magic numbers** - `p-[13px]` or `margin-top: 37px` are smells. Use the spacing scale.
- **Never duplicate style definitions** - If two components share a visual pattern, extract it to a shared token or variant.

---

## Responsive Strategy

Mobile-first is the default. Not "responsive as an afterthought" - the mobile layout is designed first, then enhanced for larger screens.

**Breakpoint philosophy:**
- Don't think in device names (phone/tablet/desktop). Think in content needs.
- A breakpoint should exist because the content breaks, not because a device exists at that width.
- Most layouts need only 2-3 breakpoints. More than 4 is a smell.

**Layout patterns by content type:**
- **Marketing/editorial**: Single column mobile, 2-column tablet, asymmetric desktop
- **Dashboard**: Stack mobile, sidebar + content desktop
- **Data tables**: Card-based mobile, table desktop
- **Forms**: Single column always (multi-column forms reduce completion rates)

---

## Performance by Default

- Lazy load images below the fold
- Use semantic HTML (`nav`, `main`, `section`, `article`, `aside`, `footer`)
- Meaningful `alt` attributes on all images (not "image" or "photo")
- Focus-visible styles on all interactive elements (accessibility is not optional)
- Prefer CSS animations over JS animations
- Avoid layout shifts - set explicit dimensions on images and dynamic content areas

---

## Additional Resources

### Reference Files

- **`references/design-system.md`** - Complete design system implementation guide with code examples for multiple frameworks, color theory, typography pairing strategies, and token architecture
- **`references/anti-slop-visual.md`** - Comprehensive visual anti-slop guide with before/after examples, the AI aesthetic taxonomy, and techniques for injecting personality into interfaces
- **`references/patterns.md`** - Component patterns, layout compositions, animation recipes, and responsive strategies with code examples
