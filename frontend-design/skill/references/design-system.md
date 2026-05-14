# Design System Implementation Guide

How to build a design system that produces distinctive, cohesive interfaces. Framework-specific examples included, principles are universal.

**Research warning**: Every code example below references specific CSS features, Tailwind config shapes, and framework APIs that may have changed since this file was written. Before implementing:
- Check your Tailwind version (v3 vs v4 have different config formats)
- Check CSS feature support on caniuse.com — features marked "needs fallback" here may now be baseline
- Check your framework version's recommended patterns — React 18 vs 19, Next.js Pages vs App Router, Vue 2 vs 3 all differ significantly
- Check if any utility shown here has a newer native CSS equivalent (e.g., `color-mix()` may replace manual HSL manipulation)

---

## Token Architecture

A design system is a tree of tokens. Raw values feed into semantic tokens, semantic tokens feed into component tokens. Never skip a level.

```
Raw Values (Level 0)
  --blue-600: 220 80% 50%
  --gray-100: 220 15% 96%

Semantic Tokens (Level 1) 
  --primary: var(--blue-600)
  --background: var(--gray-100)
  --foreground: 220 15% 10%

Component Tokens (Level 2)
  --button-bg: var(--primary)
  --button-text: var(--primary-foreground)
  --card-bg: var(--background)
```

**Why three levels?** Changing `--blue-600` to a green updates every component at once. Changing `--primary` rebrands the entire app. Changing `--button-bg` adjusts only buttons. Each level serves a different type of design decision.

---

## Color System

### Building a Palette

Start with ONE color. The primary. Everything else derives from it.

**Method: HSL Manipulation**

```css
:root {
  /* Primary - your brand color */
  --primary-h: 220;
  --primary-s: 80%;
  --primary-l: 50%;
  --primary: var(--primary-h) var(--primary-s) var(--primary-l);
  
  /* Derived from primary by shifting hue */
  --secondary-h: calc(var(--primary-h) + 30);
  --accent-h: calc(var(--primary-h) + 180);
  
  /* Neutral scale - desaturated primary (keeps warmth/coolness consistent) */
  --neutral-h: var(--primary-h);
  --neutral-s: 10%;
  --neutral-50: var(--neutral-h) var(--neutral-s) 98%;
  --neutral-100: var(--neutral-h) var(--neutral-s) 96%;
  --neutral-200: var(--neutral-h) var(--neutral-s) 90%;
  --neutral-300: var(--neutral-h) var(--neutral-s) 82%;
  --neutral-400: var(--neutral-h) var(--neutral-s) 64%;
  --neutral-500: var(--neutral-h) var(--neutral-s) 46%;
  --neutral-600: var(--neutral-h) var(--neutral-s) 33%;
  --neutral-700: var(--neutral-h) var(--neutral-s) 24%;
  --neutral-800: var(--neutral-h) var(--neutral-s) 15%;
  --neutral-900: var(--neutral-h) var(--neutral-s) 9%;
  --neutral-950: var(--neutral-h) var(--neutral-s) 5%;
}
```

**Key insight**: Neutral grays derived from your primary hue create cohesion. Pure gray (`0 0% XX%`) feels disconnected. A warm primary (hue ~30) should have warm grays. A cool primary (hue ~220) should have cool grays.

### Color Relationships

| Relationship | Hue Delta | Feel | Use Case |
|-------------|-----------|------|----------|
| Monochromatic | 0 (vary S/L) | Calm, cohesive | Professional tools, dashboards |
| Analogous | +/- 30 | Harmonious, warm | Content-heavy sites, blogs |
| Complementary | +180 | High contrast, energetic | CTAs, accent elements |
| Split-complementary | +150, +210 | Balanced contrast | Marketing pages, creative tools |
| Triadic | +120, +240 | Vibrant, playful | Consumer apps, games |

### Semantic Color Mapping

```css
:root {
  /* Surfaces */
  --background: var(--neutral-50);
  --foreground: var(--neutral-900);
  --card: 0 0% 100%;
  --card-foreground: var(--neutral-900);
  --muted: var(--neutral-100);
  --muted-foreground: var(--neutral-500);
  
  /* Interactive */
  --primary: var(--primary-h) var(--primary-s) var(--primary-l);
  --primary-foreground: 0 0% 100%;  /* or dark, depends on primary lightness */
  
  /* Feedback */
  --success: 142 72% 42%;
  --warning: 38 92% 50%;
  --error: 0 84% 60%;
  --info: 200 80% 50%;
  
  /* Borders & Dividers */
  --border: var(--neutral-200);
  --ring: var(--primary);
}
```

### Dark Mode (Not Just Inverting)

Dark mode is a separate design. Not `filter: invert()`. Not swapping black and white.

```css
.dark {
  /* Surfaces - NOT pure black, slightly tinted with primary hue */
  --background: var(--neutral-950);
  --foreground: var(--neutral-50);
  --card: var(--neutral-900);
  --card-foreground: var(--neutral-100);
  --muted: var(--neutral-800);
  --muted-foreground: var(--neutral-400);
  
  /* Primary often needs lightness bump in dark mode for contrast */
  --primary: var(--primary-h) var(--primary-s) 60%;
  --primary-foreground: var(--neutral-950);
  
  /* Borders more subtle in dark mode */
  --border: var(--neutral-800);
  
  /* Shadows use opacity, not color change */
  --shadow-color: 0 0% 0%;
}
```

**Dark mode rules:**
- Background is never `#000000`. Use `hsl(220 15% 5%)` - slightly tinted, slightly lifted.
- Text is never `#FFFFFF`. Use `hsl(220 15% 96%)` - slightly warm, easier on eyes.
- Reduce shadow intensity (increase transparency, reduce blur).
- Saturated colors often need desaturation in dark mode to avoid "glowing" effect.
- Test every component in both modes. White text on white background is the #1 dark mode bug.

---

## Typography System

### Font Pairing Strategy

Pick exactly 2 fonts, maximum 3. Each must have a clear role:

| Combination | Feel | When to use |
|------------|------|------------|
| Serif heading + Sans body | Editorial, premium | Marketing, content, luxury |
| Sans heading + Sans body (different families) | Modern, clean | SaaS, tools, tech |
| Mono heading + Sans body | Technical, distinctive | Developer tools, fintech |
| Display heading + Sans body | Bold, personality | Consumer apps, creative |
| Single font (weight variation) | Minimal, systematic | Dashboards, data-heavy apps |

**Pairing rule**: Contrast in structure, harmony in mood. A geometric sans (Outfit, Space Grotesk) pairs with a humanist sans (Source Sans, Nunito) because they differ structurally but share modern warmth.

### Type Scale

Use a ratio-based scale, not arbitrary sizes. Common ratios:

| Ratio | Name | Feel |
|-------|------|------|
| 1.125 | Major Second | Compact, data-dense |
| 1.200 | Minor Third | Balanced, general purpose |
| 1.250 | Major Third | Clear hierarchy, editorial |
| 1.333 | Perfect Fourth | Dramatic, marketing |
| 1.500 | Perfect Fifth | Very dramatic, headlines-heavy |

**Implementation:**

```css
:root {
  --font-size-base: 1rem;        /* 16px */
  --scale-ratio: 1.25;           /* Major Third */
  
  --text-xs: calc(var(--font-size-base) / var(--scale-ratio) / var(--scale-ratio));
  --text-sm: calc(var(--font-size-base) / var(--scale-ratio));
  --text-base: var(--font-size-base);
  --text-lg: calc(var(--font-size-base) * var(--scale-ratio));
  --text-xl: calc(var(--font-size-base) * var(--scale-ratio) * var(--scale-ratio));
  --text-2xl: calc(var(--font-size-base) * var(--scale-ratio) * var(--scale-ratio) * var(--scale-ratio));
  --text-3xl: calc(var(--font-size-base) * var(--scale-ratio) * var(--scale-ratio) * var(--scale-ratio) * var(--scale-ratio));
  
  /* Or simpler, pre-calculated: */
  --text-xs: 0.64rem;
  --text-sm: 0.8rem;
  --text-base: 1rem;
  --text-lg: 1.25rem;
  --text-xl: 1.563rem;
  --text-2xl: 1.953rem;
  --text-3xl: 2.441rem;
  --text-4xl: 3.052rem;
  --text-5xl: 3.815rem;
}
```

### Line Height Rules

- Body text (14-18px): `line-height: 1.5-1.6`
- Large text (20-32px): `line-height: 1.3-1.4`
- Headlines (36px+): `line-height: 1.1-1.2`
- UI labels/buttons: `line-height: 1`

**Rule**: As font size increases, line height ratio decreases. Large text with 1.5 line-height looks like double-spacing.

---

## Spacing System

Use a 4px base with a geometric scale. Every spacing value in the system must be from this scale.

```css
:root {
  --space-0: 0;
  --space-1: 0.25rem;   /* 4px */
  --space-2: 0.5rem;    /* 8px */
  --space-3: 0.75rem;   /* 12px */
  --space-4: 1rem;      /* 16px */
  --space-5: 1.25rem;   /* 20px */
  --space-6: 1.5rem;    /* 24px */
  --space-8: 2rem;      /* 32px */
  --space-10: 2.5rem;   /* 40px */
  --space-12: 3rem;     /* 48px */
  --space-16: 4rem;     /* 64px */
  --space-20: 5rem;     /* 80px */
  --space-24: 6rem;     /* 96px */
  --space-32: 8rem;     /* 128px */
}
```

**Spacing rules:**
- Related elements: `--space-2` to `--space-4` (tight grouping)
- Section internal padding: `--space-8` to `--space-12`
- Between sections: `--space-16` to `--space-24`
- Page-level vertical rhythm: `--space-24` to `--space-32`
- Never use values outside the scale. `padding: 13px` is a smell.

---

## Shadow System

Shadows communicate elevation. Define 4-5 levels:

```css
:root {
  /* Subtle - cards resting on surface */
  --shadow-sm: 
    0 1px 2px 0 hsl(var(--shadow-color) / 0.05);
  
  /* Medium - cards on hover, dropdowns */
  --shadow-md: 
    0 4px 6px -1px hsl(var(--shadow-color) / 0.07),
    0 2px 4px -2px hsl(var(--shadow-color) / 0.05);
  
  /* Prominent - modals, popovers */
  --shadow-lg: 
    0 10px 15px -3px hsl(var(--shadow-color) / 0.08),
    0 4px 6px -4px hsl(var(--shadow-color) / 0.05);
  
  /* Dramatic - floating elements */
  --shadow-xl: 
    0 20px 25px -5px hsl(var(--shadow-color) / 0.1),
    0 8px 10px -6px hsl(var(--shadow-color) / 0.05);
  
  /* Glow - using primary color for branded feel */
  --shadow-glow: 
    0 0 20px hsl(var(--primary) / 0.15),
    0 0 40px hsl(var(--primary) / 0.05);
    
  --shadow-color: var(--neutral-h) var(--neutral-s) 20%;
}
```

**Shadow rules:**
- Multiple shadow layers look more natural than a single heavy shadow.
- Shadow color should be tinted with the neutral hue, not pure black.
- In dark mode, shadows are nearly invisible. Use border or subtle background difference instead.
- A shadow increase on hover (`sm` -> `md`) creates a satisfying lift effect.

---

## Animation Tokens

Define timing and easing in one place:

```css
:root {
  /* Durations */
  --duration-fast: 100ms;     /* Hover states, micro-interactions */
  --duration-normal: 200ms;   /* Transitions, reveals */
  --duration-slow: 300ms;     /* Page transitions, complex animations */
  --duration-slower: 500ms;   /* Entrance animations */
  
  /* Easings */
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);      /* Deceleration - things arriving */
  --ease-in: cubic-bezier(0.55, 0, 1, 0.45);       /* Acceleration - things leaving */
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);   /* Symmetric - morphing */
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1); /* Overshoot - playful UI */
  
  /* Named transitions */
  --transition-colors: color var(--duration-fast) var(--ease-out),
                       background-color var(--duration-fast) var(--ease-out),
                       border-color var(--duration-fast) var(--ease-out);
  --transition-transform: transform var(--duration-normal) var(--ease-out);
  --transition-shadow: box-shadow var(--duration-normal) var(--ease-out);
  --transition-opacity: opacity var(--duration-normal) var(--ease-out);
}
```

**Animation principles:**
- Elements entering the viewport: `ease-out` (they decelerate into place)
- Elements leaving: `ease-in` (they accelerate away)
- Hover/state changes: `ease-out`, fast duration
- Never animate `width`, `height`, `top`, `left` - use `transform` and `opacity` only (GPU accelerated)
- Spring easing (`ease-spring`) adds personality but use sparingly - 1-2 elements per page max

---

## Tailwind CSS Implementation (Most Common)

### tailwind.config.ts

```typescript
import type { Config } from "tailwindcss";

const config: Config = {
  darkMode: "class",
  content: ["./src/**/*.{ts,tsx,js,jsx}"],
  theme: {
    extend: {
      colors: {
        border: "hsl(var(--border))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
        success: "hsl(var(--success))",
        warning: "hsl(var(--warning))",
        error: "hsl(var(--error))",
      },
      fontFamily: {
        heading: ["var(--font-heading)"],
        body: ["var(--font-body)"],
        mono: ["var(--font-mono)"],
      },
      boxShadow: {
        sm: "var(--shadow-sm)",
        md: "var(--shadow-md)",
        lg: "var(--shadow-lg)",
        xl: "var(--shadow-xl)",
        glow: "var(--shadow-glow)",
      },
      borderRadius: {
        DEFAULT: "var(--radius)",
      },
      keyframes: {
        "fade-in": {
          from: { opacity: "0", transform: "translateY(8px)" },
          to: { opacity: "1", transform: "translateY(0)" },
        },
        "slide-in-right": {
          from: { transform: "translateX(100%)" },
          to: { transform: "translateX(0)" },
        },
      },
      animation: {
        "fade-in": "fade-in var(--duration-slow) var(--ease-out) forwards",
        "slide-in-right": "slide-in-right var(--duration-slow) var(--ease-out)",
      },
    },
  },
};

export default config;
```

### globals.css / index.css

```css
@import url('https://fonts.googleapis.com/css2?family=...');

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --font-heading: "Your Heading Font", sans-serif;
    --font-body: "Your Body Font", sans-serif;
    --font-mono: "JetBrains Mono", monospace;

    /* Your full color system goes here */
    --background: 220 15% 97%;
    --foreground: 220 15% 9%;
    /* ... all semantic tokens ... */
    
    --radius: 0.5rem;
  }
  
  .dark {
    --background: 220 15% 5%;
    --foreground: 220 15% 96%;
    /* ... dark overrides ... */
  }
  
  * {
    border-color: hsl(var(--border));
  }
  
  body {
    background-color: hsl(var(--background));
    color: hsl(var(--foreground));
    font-family: var(--font-body);
  }
}
```

---

## Vanilla CSS Implementation

For projects not using Tailwind, the same token system in pure CSS:

```css
:root {
  /* Colors */
  --primary: 220 80% 50%;
  --primary-foreground: 0 0% 100%;
  /* ... full token set ... */
}

/* Utility classes (create only what you use) */
.text-primary { color: hsl(var(--primary)); }
.bg-primary { background-color: hsl(var(--primary)); }
.text-muted { color: hsl(var(--muted-foreground)); }

/* Component-level tokens */
.btn {
  --btn-bg: var(--primary);
  --btn-text: var(--primary-foreground);
  --btn-radius: var(--radius);
  --btn-padding-x: var(--space-4);
  --btn-padding-y: var(--space-2);
  
  background-color: hsl(var(--btn-bg));
  color: hsl(var(--btn-text));
  border-radius: var(--btn-radius);
  padding: var(--btn-padding-y) var(--btn-padding-x);
  transition: var(--transition-colors), var(--transition-shadow);
}

.btn:hover {
  filter: brightness(1.1);
  box-shadow: var(--shadow-md);
}

.btn--secondary {
  --btn-bg: var(--secondary);
  --btn-text: var(--secondary-foreground);
}
```
