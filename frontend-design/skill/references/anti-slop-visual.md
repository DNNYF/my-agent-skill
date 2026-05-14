# Visual Anti-Slop Guide

A comprehensive catalog of what makes AI-generated frontends look AI-generated, and how to fix every pattern. This document exists because AI defaults to safe, generic, template-marketplace aesthetics that any experienced designer can spot instantly.

**Research mandate**: The "AI aesthetic" evolves as AI tools improve. The purple-gradient-with-cards look described below was the dominant AI tell in 2023-2024. By the time you read this, AI tools may have learned to avoid that specific pattern — but they'll have new tells. Before building, search for current discussions about AI-generated design patterns: `"AI generated website tells [current year]"`, `"how to spot AI design"`. The patterns below are foundational, but stay aware of new ones.

Additionally: the font recommendations, layout techniques, and design trends referenced here reflect a specific moment. Research what's current:
- Font trends shift. A font recommended here may now be overused (the way Inter became ubiquitous).
- New CSS capabilities enable layouts that weren't possible when this was written.
- Design trends cycle. What reads as "fresh" today may read as "dated" in 12 months. Check Awwwards, SiteInspire, or Godly for current reference points.

---

## The AI Aesthetic Taxonomy

AI-generated frontends share recognizable traits. Each trait below is cataloged with what the AI produces by default, why it looks artificial, and what a human designer would do instead.

### 1. The Purple Gradient Problem

**AI default**: Hero section with a purple-to-blue or purple-to-pink gradient. White text. Centered headline. "Get Started Free" button.

**Why it's a tell**: Every AI coding tool defaults to purple/indigo as the primary color. It's become the "AI starter kit" color the same way Bootstrap blue was the "I used a framework" tell in 2015.

**Expert approach**: 
- Choose a color that reflects the product's domain. Fintech? Deep navy or forest green. Health? Warm coral or sage. Creative tool? A specific, non-generic hue - burnt orange, teal, olive.
- If you must use a gradient, make it subtle. Background gradients should be barely perceptible - two shades of the same hue, not a rainbow.
- Solid colors with texture (noise overlay, subtle pattern, photographic background) read more sophisticated than gradients.

### 2. The Card Grid of Sameness

**AI default**: Three identical cards in a row. Each has: icon (from Lucide/Heroicons), heading, 2-line paragraph. Equal height, equal padding, equal everything.

**Why it's a tell**: Perfect symmetry is boring and artificial. Real design uses hierarchy - one card is more important, one is visually different, or the layout itself breaks the grid.

**Expert approach**:
- Vary card sizes. One large feature card + two smaller supporting cards.
- Use different card treatments for different content types. A testimonial card looks different from a feature card looks different from a pricing card.
- Break the grid. A card that spans two columns. A card that overlaps the section boundary. A card rotated 1-2 degrees.
- Consider: does this content even need cards? A simple text list with good typography is often better than cards.

### 3. The Icon Parade

**AI default**: Every feature/benefit has a matching icon from the same icon set. Icons are in circles or rounded squares. All the same size, same color, same weight.

**Why it's a tell**: Humans don't icon-ify everything. Icons are most powerful when used sparingly as visual anchors, not as decoration for every bullet point.

**Expert approach**:
- Use icons only where they add meaning that text alone can't convey.
- If using multiple icons, vary their presentation: some outlined, some filled, some at different sizes, some as background elements rather than inline.
- Consider illustrations, photos, or simple colored shapes instead of icons.
- One strong illustration beats 6 generic icons.

### 4. The Uniform Spacing Trap

**AI default**: Every section has the same padding. Every gap between elements is the same. The page has a metronomic rhythm - section, space, section, space.

**Why it's a tell**: Real editorial and web design uses variable rhythm. A tight cluster of related items followed by generous whitespace creates visual breathing room and hierarchy.

**Expert approach**:
- Group related content tightly (12-16px gaps).
- Separate sections generously (64-128px).
- Create intentional density contrast: a compact info section followed by a spacious hero-style callout.
- Let important content breathe more than supporting content.

### 5. The Font Monotony

**AI default**: Inter, system-ui, or Geist everywhere. Default weights (400 for body, 600/700 for headings). Default sizes. No personality.

**Why it's a tell**: Typography IS design. Using the default system font is like wearing a plain gray t-shirt to every occasion - fine, but not designed.

**Expert approach**:
- Choose a heading font with personality. Examples:
  - **Playfair Display** - elegant, editorial
  - **Space Grotesk** - technical, geometric, modern
  - **Cabinet Grotesk** - friendly, contemporary
  - **Instrument Serif** - refined, minimal
  - **Sora** - geometric, futuristic
  - **DM Sans** - warm, approachable but professional
- Use weight contrast aggressively. A 300-weight heading at 48px next to a 500-weight subheading at 16px creates drama.
- Letter-spacing is a tool. Slightly tracked-out uppercase labels. Slightly tightened large headlines.
- Don't use more than 3 weights of any single font. If you need 5 weights, your hierarchy is too complex.

### 6. The Flat Surface Syndrome

**AI default**: White cards on light gray background. Or dark cards on darker background. Completely flat. No depth, no layering, no overlap.

**Why it's a tell**: Physical objects have depth. Screens that feel alive use layering, overlap, and subtle elevation to create spatial relationships.

**Expert approach**:
- Overlap elements intentionally. An image that breaks out of its container by 20px. A badge that sits half in, half out of a card.
- Use subtle background textures. A noise overlay at 2-3% opacity. A very subtle grid pattern. A gradient mesh.
- Create depth with multiple background layers. A section with a colored background behind a white content area.
- Glassmorphism used sparingly (one element per page max) adds sophistication. Used everywhere, it's a gimmick.

### 7. The Animation Desert (or Excess)

**AI default**: Either zero animation (static, lifeless) or every element has a bounce/fade/slide on scroll (distracting, performative).

**Why it's a tell**: No animation feels cheap. Too much animation feels like a tech demo. Human designers use animation surgically.

**Expert approach**:
- **Page load**: Stagger-fade the hero content (heading first, then subheading, then CTA). One animation, 3 elements, done.
- **Scroll**: Fade-in-up for content entering the viewport. Subtle (8-16px translate, not 50px). Only for content below the fold.
- **Interaction**: Hover states with slight scale/shadow. Button press with slight depression. These are felt, not noticed.
- **Transition**: Page transitions or view transitions add polish. But only if the framework supports them cleanly.
- **Never**: Parallax on more than one layer. Rotating elements. Bouncing elements. Pulsing CTAs.

---

## Before/After Patterns

### Hero Section

**AI Slop:**
```
[Centered]
[Gradient Background purple → blue]
"Transform Your Workflow with AI-Powered Solutions"
"Our cutting-edge platform leverages the latest technology to 
streamline your processes and boost productivity."
[ Get Started ]  [ Learn More ]
```

**Expert:**
```
[Left-aligned, asymmetric layout]
[Solid dark background with subtle grain texture]
[Large serif heading, 64px, weight 300]
"Ship invoices 
in 30 seconds."
[Sans body, 18px, muted color, max-width 420px]  
"Stop chasing payments. Bento sends polished invoices 
the moment you finish the work."
[ Start free — no card needed ]
[Right side: actual product screenshot, slightly rotated, with shadow]
```

**Why the expert version works**: Specific (30 seconds, not "transform"). Shows the product. Asymmetric layout. Typography contrast. One CTA (not two competing buttons). Texture on background.

### Feature Section

**AI Slop:**
```
"Our Features"

[Icon] Feature 1       [Icon] Feature 2       [Icon] Feature 3
Description text        Description text        Description text
that is generic         that is generic         that is generic

[Icon] Feature 4       [Icon] Feature 5       [Icon] Feature 6
Description text        Description text        Description text
that is generic         that is generic         that is generic
```

**Expert:**
```
[No "Our Features" heading - the content speaks]

[Left: Large product screenshot showing the feature in action]
[Right: ]
  "Auto-categorize expenses"
  [Small text] Snap a receipt. Bento reads the vendor, amount, 
  and category. Accuracy: 94% on first try.
  
  [Below, smaller, tighter spacing:]
  ✓ Works with paper and digital receipts
  ✓ Learns your categories over time
  ✓ Flags duplicates automatically

[Next section: reversed layout - text left, screenshot right]
```

**Why the expert version works**: Shows, doesn't tell. One feature at a time with depth. Real numbers (94%). Layout variety. No icon grid.

### Testimonial Section

**AI Slop:**
```
"What Our Customers Say"

[Card]                  [Card]                  [Card]
"Great product!"        "Love it!"              "Highly recommend!"
★★★★★                  ★★★★★                  ★★★★★
- John D.              - Sarah M.              - Mike R.
  CEO                    Designer                Developer
```

**Expert:**
```
[Full-width, single testimonial, large typography]

[48px, serif italic]
"We stopped losing invoices to email chains. 
Last quarter we collected $34K faster than the 
quarter before."

[16px, sans, muted]
Rina Wijaya · Finance Lead at Nexacraft
[Small company logo, grayscale]

[Subtle navigation dots indicating more testimonials]
```

**Why the expert version works**: One powerful quote beats three weak ones. Specific result ($34K). Real-sounding name. Company context. Large typography makes it feel important.

---

## Quick Reference: AI Tells to Avoid

| AI Tell | Fix |
|---------|-----|
| Purple/indigo primary color | Choose a domain-appropriate, non-default color |
| Perfectly symmetrical 3-column cards | Vary sizes, break the grid, question if cards are even needed |
| Icons from one set, all same size | Use sparingly, vary presentation, consider illustrations |
| "Transform Your X with Y" headline | Specific benefit in specific timeframe |
| Two competing CTAs ("Get Started" + "Learn More") | One primary CTA per section |
| Section titles like "Our Features", "Why Choose Us" | Let content speak, or use benefit-oriented titles |
| Uniform section padding throughout | Variable rhythm: tight groups, generous separators |
| Inter/system font everywhere | Chosen fonts with personality matching the brand |
| Flat white cards on flat gray | Layering, overlap, texture, depth |
| Stock illustration of happy people using laptops | Product screenshots, custom illustrations, or no image |
| "Cutting-edge", "innovative", "revolutionary" copy | Specific, factual, benefit-oriented language |
| Perfect rounded corners on everything | Vary radii. Sharp corners can be intentional. |
| `max-width: 1200px` centered on every section | Full-bleed sections, asymmetric layouts, varied widths |
| Footer with 4 columns of links | Only include links people actually click |

---

## The Personality Injection Checklist

After building an interface, run through these to add the 20% that separates "competent" from "distinctive":

1. **One visual surprise** - An element that breaks the pattern. Rotated card. Oversized number. Handwritten annotation. Something that makes someone pause.

2. **One color moment** - A single use of a vibrant accent color that draws the eye exactly where it should go. Everything else is restrained.

3. **One typographic choice that has opinion** - A headline in a serif when everything else is sans. A number in a display font. Something that says "a human chose this."

4. **One interaction that delights** - A hover effect that reveals information. A scroll-triggered transition that feels smooth. A cursor change on a specific element.

5. **One piece of real content** - Replace at least one "Lorem ipsum" block with real, specific copy. Real content informs design decisions that placeholder text hides.
