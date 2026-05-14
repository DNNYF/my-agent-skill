# Component Patterns & Layout Strategies

Reusable patterns for building interfaces that are distinctive, responsive, and maintainable. Every pattern uses design system tokens - no magic numbers, no raw colors.

**Before using any pattern below**: Verify the CSS features it relies on are current and check for native alternatives that may have landed since this file was written.

Key things to check:
- **Container queries** (`@container`) — Baseline since Feb 2023, but new features like container query units (`cqi`, `cqb`) and style queries may have shipped. Check MDN.
- **View Transitions API** — May now be stable enough to replace JS-based page transitions shown here.
- **Anchor positioning** (`anchor()`, `position-anchor`) — May replace manual tooltip/popover positioning patterns.
- **`@scope`** — May replace some BEM/namespace patterns for component scoping.
- **Scroll-driven animations** (`animation-timeline: scroll()`) — May replace the JS Intersection Observer scroll-reveal pattern shown below.
- **`@starting-style`** — May simplify entry animations that currently need `@keyframes`.

When a native CSS solution exists for something previously requiring JS or a workaround, prefer the native solution. Less code, better performance, fewer dependencies.

---

## Layout Patterns

### 1. The Asymmetric Hero

The most overused AI layout is centered hero text with equal margins. Break it.

```css
.hero {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--space-12);
  align-items: center;
  min-height: 80vh;
  padding: var(--space-16) var(--space-8);
}

.hero__content {
  max-width: 32rem; /* Constrain text, not the grid */
}

.hero__visual {
  position: relative;
}

.hero__visual img {
  border-radius: var(--radius);
  box-shadow: var(--shadow-xl);
  transform: rotate(-2deg); /* Subtle tilt adds life */
}

/* Mobile: stack, image first (visual hook) */
@media (max-width: 768px) {
  .hero {
    grid-template-columns: 1fr;
    min-height: auto;
    padding: var(--space-8) var(--space-4);
  }
  
  .hero__visual {
    order: -1; /* Image above text on mobile */
  }
  
  .hero__visual img {
    transform: none; /* Remove tilt on small screens */
  }
}
```

### 2. The Bento Grid

Instead of uniform card grids, use a bento-box layout where items have different sizes.

```css
.bento {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(180px, auto);
  gap: var(--space-4);
}

.bento__item--wide { grid-column: span 2; }
.bento__item--tall { grid-row: span 2; }
.bento__item--featured { 
  grid-column: span 2; 
  grid-row: span 2; 
}

/* Tablet: 2 columns */
@media (max-width: 1024px) {
  .bento {
    grid-template-columns: repeat(2, 1fr);
  }
  .bento__item--wide { grid-column: span 2; }
  .bento__item--tall { grid-row: span 1; }
  .bento__item--featured { grid-column: span 2; }
}

/* Mobile: single column */
@media (max-width: 640px) {
  .bento {
    grid-template-columns: 1fr;
  }
  .bento__item--wide,
  .bento__item--featured { grid-column: span 1; }
}
```

### 3. The Broken Grid

Elements that intentionally break alignment create visual interest.

```css
.broken-grid {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  gap: var(--space-4);
}

.broken-grid__text {
  grid-column: 1 / 6;
  padding-top: var(--space-16); /* Offset vertically */
}

.broken-grid__image {
  grid-column: 5 / 13; /* Overlaps text column */
  margin-top: calc(var(--space-8) * -1); /* Negative margin for overlap */
  z-index: 1;
}

.broken-grid__accent {
  grid-column: 2 / 4;
  margin-top: calc(var(--space-12) * -1);
  z-index: 2;
}
```

### 4. The Dashboard Shell

Sidebar + main content pattern that scales from mobile to desktop.

```css
.dashboard {
  display: grid;
  grid-template-columns: auto 1fr;
  grid-template-rows: auto 1fr;
  height: 100vh;
}

.dashboard__header {
  grid-column: 1 / -1;
  border-bottom: 1px solid hsl(var(--border));
  padding: var(--space-3) var(--space-6);
}

.dashboard__sidebar {
  width: 260px;
  border-right: 1px solid hsl(var(--border));
  padding: var(--space-4);
  overflow-y: auto;
}

.dashboard__main {
  padding: var(--space-6);
  overflow-y: auto;
  background: hsl(var(--muted));
}

/* Mobile: sidebar becomes bottom sheet or hamburger */
@media (max-width: 768px) {
  .dashboard {
    grid-template-columns: 1fr;
  }
  
  .dashboard__sidebar {
    position: fixed;
    inset: 0;
    width: 100%;
    z-index: 50;
    transform: translateX(-100%);
    transition: transform var(--duration-slow) var(--ease-out);
  }
  
  .dashboard__sidebar[data-open="true"] {
    transform: translateX(0);
  }
}
```

---

## Component Patterns

### Buttons with Real Variants

Not just "primary" and "secondary." Create variants that serve actual use cases.

```tsx
// Using CVA (class-variance-authority) with Tailwind
import { cva, type VariantProps } from "class-variance-authority";

const button = cva(
  // Base styles - all buttons share these
  "inline-flex items-center justify-center font-medium transition-all focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        // Primary action - filled, prominent
        primary: "bg-primary text-primary-foreground shadow-sm hover:shadow-md active:shadow-sm",
        
        // Secondary action - outlined, less prominent
        secondary: "border border-border bg-transparent text-foreground hover:bg-muted",
        
        // Destructive - red, for dangerous actions
        destructive: "bg-error text-white hover:bg-error/90",
        
        // Ghost - no background, minimal
        ghost: "text-muted-foreground hover:text-foreground hover:bg-muted",
        
        // Link-style - looks like a link, behaves like a button
        link: "text-primary underline-offset-4 hover:underline p-0 h-auto",
      },
      size: {
        sm: "h-8 px-3 text-sm rounded-md gap-1.5",
        md: "h-10 px-4 text-sm rounded-lg gap-2",
        lg: "h-12 px-6 text-base rounded-lg gap-2.5",
        icon: "h-10 w-10 rounded-lg",
      },
    },
    defaultVariants: {
      variant: "primary",
      size: "md",
    },
  }
);
```

### Cards with Personality

Cards that don't look like every other card on the internet.

```css
/* The Floating Card - lifts on hover */
.card-float {
  background: hsl(var(--card));
  border: 1px solid hsl(var(--border));
  border-radius: var(--radius);
  padding: var(--space-6);
  transition: var(--transition-transform), var(--transition-shadow);
}

.card-float:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-lg);
}

/* The Accent Card - colored left border */
.card-accent {
  background: hsl(var(--card));
  border-left: 3px solid hsl(var(--primary));
  padding: var(--space-6);
  border-radius: 0 var(--radius) var(--radius) 0;
}

/* The Glass Card - translucent with backdrop blur */
.card-glass {
  background: hsl(var(--card) / 0.6);
  backdrop-filter: blur(12px);
  border: 1px solid hsl(var(--border) / 0.5);
  border-radius: var(--radius);
  padding: var(--space-6);
}

/* The Gradient Border Card - subtle gradient on border */
.card-gradient {
  position: relative;
  background: hsl(var(--card));
  border-radius: var(--radius);
  padding: var(--space-6);
}

.card-gradient::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1px;
  background: linear-gradient(
    135deg,
    hsl(var(--primary) / 0.3),
    hsl(var(--accent) / 0.1)
  );
  -webkit-mask: 
    linear-gradient(#fff 0 0) content-box,
    linear-gradient(#fff 0 0);
  mask: 
    linear-gradient(#fff 0 0) content-box,
    linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
}
```

### Input Fields

Inputs that feel polished, not default.

```css
.input {
  width: 100%;
  height: 2.75rem;
  padding: var(--space-2) var(--space-3);
  background: hsl(var(--background));
  border: 1px solid hsl(var(--border));
  border-radius: var(--radius);
  font-size: var(--text-sm);
  color: hsl(var(--foreground));
  transition: var(--transition-colors);
}

.input::placeholder {
  color: hsl(var(--muted-foreground));
}

.input:hover {
  border-color: hsl(var(--muted-foreground));
}

.input:focus {
  outline: none;
  border-color: hsl(var(--primary));
  box-shadow: 0 0 0 3px hsl(var(--primary) / 0.1);
}

.input--error {
  border-color: hsl(var(--error));
}

.input--error:focus {
  box-shadow: 0 0 0 3px hsl(var(--error) / 0.1);
}
```

---

## Animation Recipes

### Staggered Fade-In (Page Load)

The most impactful, least intrusive page-load animation. Hero content fades in sequentially.

```css
.stagger-fade > * {
  opacity: 0;
  transform: translateY(12px);
  animation: fade-in var(--duration-slow) var(--ease-out) forwards;
}

.stagger-fade > *:nth-child(1) { animation-delay: 0ms; }
.stagger-fade > *:nth-child(2) { animation-delay: 80ms; }
.stagger-fade > *:nth-child(3) { animation-delay: 160ms; }
.stagger-fade > *:nth-child(4) { animation-delay: 240ms; }

@keyframes fade-in {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  .stagger-fade > * {
    opacity: 1;
    transform: none;
    animation: none;
  }
}
```

### Scroll Reveal

Fade in elements as they enter the viewport. CSS-only with `@scroll-timeline` or use Intersection Observer.

```javascript
// Lightweight scroll reveal - no library needed
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        entry.target.classList.add("revealed");
        observer.unobserve(entry.target); // Once only
      }
    });
  },
  { threshold: 0.15, rootMargin: "0px 0px -40px 0px" }
);

document.querySelectorAll("[data-reveal]").forEach((el) => {
  observer.observe(el);
});
```

```css
[data-reveal] {
  opacity: 0;
  transform: translateY(16px);
  transition: opacity var(--duration-slow) var(--ease-out),
              transform var(--duration-slow) var(--ease-out);
}

[data-reveal].revealed {
  opacity: 1;
  transform: translateY(0);
}

/* Stagger children */
[data-reveal-stagger] > * {
  opacity: 0;
  transform: translateY(16px);
  transition: opacity var(--duration-slow) var(--ease-out),
              transform var(--duration-slow) var(--ease-out);
}

[data-reveal-stagger].revealed > *:nth-child(1) { transition-delay: 0ms; }
[data-reveal-stagger].revealed > *:nth-child(2) { transition-delay: 60ms; }
[data-reveal-stagger].revealed > *:nth-child(3) { transition-delay: 120ms; }
[data-reveal-stagger].revealed > *:nth-child(4) { transition-delay: 180ms; }

[data-reveal-stagger].revealed > * {
  opacity: 1;
  transform: translateY(0);
}
```

### Hover Micro-Interactions

Subtle, professional hover effects.

```css
/* Card lift */
.hoverable-card {
  transition: var(--transition-transform), var(--transition-shadow);
}
.hoverable-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

/* Button press */
.pressable:active {
  transform: scale(0.97);
}

/* Link underline grow */
.link-grow {
  position: relative;
  text-decoration: none;
}
.link-grow::after {
  content: "";
  position: absolute;
  bottom: -2px;
  left: 0;
  width: 0;
  height: 2px;
  background: hsl(var(--primary));
  transition: width var(--duration-normal) var(--ease-out);
}
.link-grow:hover::after {
  width: 100%;
}

/* Image zoom in container */
.image-zoom {
  overflow: hidden;
  border-radius: var(--radius);
}
.image-zoom img {
  transition: transform var(--duration-slow) var(--ease-out);
}
.image-zoom:hover img {
  transform: scale(1.05);
}
```

---

## Responsive Patterns

### Container Queries Over Media Queries

When possible, size components based on their container, not the viewport:

```css
.card-container {
  container-type: inline-size;
}

.card {
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
}

/* When container is wide enough, go horizontal */
@container (min-width: 400px) {
  .card {
    flex-direction: row;
    align-items: center;
  }
}
```

### Fluid Typography

Instead of fixed font sizes at breakpoints, use fluid scaling:

```css
/* Scales from 16px at 320px viewport to 20px at 1200px viewport */
.fluid-body {
  font-size: clamp(1rem, 0.909rem + 0.45vw, 1.25rem);
}

/* Scales from 32px to 64px */
.fluid-heading {
  font-size: clamp(2rem, 1.273rem + 3.636vw, 4rem);
}

/* Scales from 48px to 96px - hero headlines */
.fluid-display {
  font-size: clamp(3rem, 1.909rem + 5.455vw, 6rem);
}
```

### Responsive Spacing

Spacing should also scale with viewport:

```css
/* Section padding that grows with viewport */
.section {
  padding-block: clamp(var(--space-12), 8vw, var(--space-24));
  padding-inline: clamp(var(--space-4), 5vw, var(--space-16));
}
```

---

## Background Texture Recipes

### Noise Overlay

Adds subtle texture to flat backgrounds:

```css
.noise-bg::after {
  content: "";
  position: fixed;
  inset: 0;
  pointer-events: none;
  opacity: 0.03;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  z-index: 1000;
}
```

### Dot Grid

```css
.dot-grid {
  background-image: radial-gradient(
    hsl(var(--border)) 1px,
    transparent 1px
  );
  background-size: 24px 24px;
}
```

### Gradient Mesh (Subtle)

```css
.gradient-mesh {
  background-color: hsl(var(--background));
  background-image: 
    radial-gradient(at 20% 20%, hsl(var(--primary) / 0.05) 0, transparent 50%),
    radial-gradient(at 80% 40%, hsl(var(--accent) / 0.05) 0, transparent 50%),
    radial-gradient(at 40% 80%, hsl(var(--secondary) / 0.05) 0, transparent 50%);
}
```
