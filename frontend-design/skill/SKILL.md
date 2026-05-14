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

## Research-First Protocol - Hard Rules

Your training data contains CSS properties, framework APIs, component library patterns, and browser capabilities that may be outdated, deprecated, or superseded. Frontend moves faster than any other domain — a pattern that was cutting-edge 6 months ago might be natively supported in CSS today, or the library you remember might have released a breaking major version.

### When to Research (Non-Negotiable)

1. **CSS features** — Before using a workaround or polyfill, check if the feature is now natively supported. Container queries, `:has()`, `color-mix()`, `@layer`, `@scope`, view transitions, anchor positioning — CSS ships new features constantly. Your training may not know about features that are now baseline.
2. **Component libraries** — Before recommending or using shadcn/ui, Radix, Headless UI, Ark UI, etc. Check: current version, API changes, new components added, components deprecated. shadcn/ui in particular evolves rapidly.
3. **Framework patterns** — Before writing React, Vue, Svelte, or framework-specific code. Check: Is this the current recommended pattern? React Server Components changed how data fetching works. Vue 3 Composition API replaced Options API as default. SvelteKit's routing changed. Don't write code for the version in your training.
4. **Font availability** — Before recommending Google Fonts or any web font. Check: Is the font still available? Are there newer variable font versions? Has the font's license changed?
5. **Browser support** — Before claiming a CSS feature "needs a fallback." Check caniuse.com equivalent data. Many features that needed fallbacks 12 months ago are now baseline across all modern browsers.
6. **Tailwind / framework versions** — Before using Tailwind classes, utility patterns, or config shapes. Tailwind v4 changed significantly from v3. Check which version the project uses and write for that version.

### How to Research

- **MDN / web.dev** — For CSS and browser API currency. These are always authoritative and current.
- **GitHub releases** — For component libraries and frameworks. Check the latest release, migration guides, breaking changes.
- **caniuse.com** — For browser support. Never claim "not widely supported" from memory.
- **npm trends / bundlephobia** — For comparing libraries. Check bundle size, download trends, last publish date.
- **Official docs** — Always read them. Never rely on your memory of docs from training. API surfaces change between minor versions.

### The Frontend Freshness Traps

| Trap | Example | Consequence |
|------|---------|-------------|
| Using old CSS workaround | Writing JS for container queries when `@container` is now baseline | Unnecessary complexity, worse performance |
| Outdated component API | Using shadcn Button with old prop interface | Build errors, wrong patterns copied everywhere |
| Deprecated framework pattern | Using `getStaticProps` in Next.js App Router project | Code works but uses legacy system, misses server component benefits |
| Stale library recommendation | Suggesting `styled-components` without noting it's in maintenance mode | User adopts a library with declining ecosystem support |
| Wrong Tailwind version | Writing v3 config syntax for a v4 project | Config doesn't work, classes resolve wrong |
| Missing native CSS | Using a JS animation library for something CSS `@keyframes` + `animation-timeline` handles natively | Extra dependency, worse performance, more code |

### Research in Practice

When building a design system or component, verify before writing:

**Bad (training-data-only):**
```css
/* Container queries need a polyfill */
.card-container { contain: layout inline-size; }
```

**Good (researched):**
```css
/* container queries: baseline since Feb 2023, 92%+ global support */
.card-container { container-type: inline-size; }

@container (min-width: 400px) {
  .card { flex-direction: row; }
}
```

**Bad (training-data-only):**
> "Use Framer Motion for animations."

**Good (researched):**
> "Use Motion (formerly Framer Motion, rebranded in v11). Current version: 11.x. Still the most capable React animation library. Alternative: CSS-only animations handle 80% of cases — use Motion only for gesture-based, layout, or shared-layout animations that CSS can't do natively."

For the complete visual anti-slop guide, consult `references/anti-slop-visual.md`.

---

## Additional Resources

### Reference Files

- **`references/design-system.md`** - Complete design system implementation guide with code examples for multiple frameworks, color theory, typography pairing strategies, and token architecture
- **`references/anti-slop-visual.md`** - Comprehensive visual anti-slop guide with before/after examples, the AI aesthetic taxonomy, and techniques for injecting personality into interfaces
- **`references/patterns.md`** - Component patterns, layout compositions, animation recipes, and responsive strategies with code examples
