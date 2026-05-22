# DESIGN.md

## Purpose

This file defines the visual design rules for MadAI-related interfaces and assets.

Use these instructions whenever you create or modify:

* UI components
* landing pages
* event pages
* visual cards
* social media assets
* presentation visuals
* illustrations
* CSS or Tailwind styles
* typography, layout, spacing, or color decisions

Treat these rules as the default visual system unless the user explicitly asks for a different style.

---

## How to load this file from CLAUDE.md

Add this line to `CLAUDE.md`:

```md
Follow the project design guidelines in @DESIGN.md whenever creating or modifying UI, visual assets, landing pages, slides, images, components, CSS, Tailwind classes, or design-related copy.
```

---

## Brand foundations

### Colors

Use this palette as the primary brand palette:

```css
--madai-primary: #2a6f97;
--madai-light: #e2eef2;
```

Usage:

* Use `#2a6f97` as the main brand color.
* Use `#e2eef2` as a soft background, secondary surface, subtle section background, or light accent.
* Prefer white or very light backgrounds when readability matters.
* Avoid introducing unrelated accent colors unless they are needed for semantic states such as success, warning, or error.
* Do not use random blues. If a blue is needed, start from `#2a6f97`.

---

## Typography

### Heading typography

Use `Crimson Text` for titles and main headings.

Allowed weights:

* Regular
* Bold

Use `Crimson Text Bold` for:

* page titles
* section titles
* major visual emphasis

Use `Crimson Text Regular` for:

* softer editorial headings
* secondary titles
* elegant title treatments

### Body and UI typography

Use `Open Sans` for all non-heading elements:

* body text
* navigation
* buttons
* labels
* cards
* captions
* metadata
* forms
* lists
* utility text

Use regular weight by default. Use heavier weights only when hierarchy or readability requires it.

### Font fallback

If the exact fonts are not available, use sensible fallbacks:

```css
font-family: "Crimson Text", Georgia, serif;
font-family: "Open Sans", Arial, sans-serif;
```

Do not replace `Crimson Text` headings with generic sans-serif headings unless there is a technical limitation.

---

## Visual tone

MadAI visuals should feel:

* clear
* calm
* technical but approachable
* community-oriented
* lightweight
* editorial rather than corporate
* modern without looking like a generic SaaS template

Avoid:

* excessive gradients
* dark cyberpunk aesthetics
* heavy shadows
* overly corporate stock-style layouts
* aggressive neon colors
* cluttered UI
* unnecessary visual noise

The brand should feel like a thoughtful AI engineering community, not like a crypto landing page that found LinkedIn.

---

## Layout principles

Prefer clean, spacious layouts.

Use:

* generous whitespace
* clear visual hierarchy
* simple sections
* readable text blocks
* restrained use of icons
* soft backgrounds using `#e2eef2`
* brand accents using `#2a6f97`

Avoid dense layouts unless the user explicitly asks for a compact format.

For landing pages or event pages:

* Use `Open Sans` for supporting copy.
* Use `#2a6f97` for primary actions, links, highlights, or key visual elements.
* Use `#e2eef2` for background blocks, cards, or subtle separation.
* Keep calls to action clear and direct.

---

## Component rules

### Buttons

Primary buttons:

* background: `#2a6f97`
* text: white
* typography: `Open Sans`
* shape: slightly rounded, not overly pill-shaped unless the existing UI already uses that style

Secondary buttons:

* background: white or transparent
* border: `#2a6f97`
* text: `#2a6f97`

Avoid:

* random accent colors
* excessive shadows
* oversized pill buttons unless already part of the existing design system

### Cards

Cards should be simple and readable.

Prefer:

* white background
* subtle border
* optional light background sections using `#e2eef2`
* minimal shadow
* clear spacing

Avoid:

* heavy shadows
* saturated backgrounds
* noisy decorative patterns
* too many competing card styles

### Links

Use `#2a6f97` for links.

Links should be visibly interactive through:

* underline
* hover state
* clear contrast

Do not rely only on subtle color differences.

---

## Accessibility rules

Always preserve readability and contrast.

Before finalizing UI changes:

* Ensure text over `#2a6f97` is white or very light.
* Avoid placing low-contrast grey text over `#e2eef2`.
* Keep body text readable at normal sizes.
* Do not use typography as the only way to communicate meaning.
* Maintain visible focus states for interactive elements.
* Check that buttons, links, and form controls are usable from keyboard navigation.

---

## Implementation guidance

When working with CSS, define reusable tokens instead of scattering raw values:

```css
:root {
  --madai-primary: #2a6f97;
  --madai-light: #e2eef2;
  --madai-heading-font: "Crimson Text", Georgia, serif;
  --madai-body-font: "Open Sans", Arial, sans-serif;
}
```

When working with Tailwind, prefer project-level theme tokens if available.

If there is no theme configuration yet, use the exact hex values directly but avoid multiplying near-duplicates.

Example:

```tsx
<h1 className="font-serif text-4xl font-bold text-[#2a6f97]">
  MadAI
</h1>

<p className="font-sans text-base">
  Community-driven conversations about AI engineering.
</p>
```

Only add font-loading code if the project does not already load fonts.

---

## Design review checklist

Before finishing any visual task, check:

* Are headings using `Crimson Text`?
* Are body and UI elements using `Open Sans`?
* Is `#2a6f97` the main brand color?
* Is `#e2eef2` used only as a soft supporting color?
* Is the design clean, calm, and community-oriented?
* Is the layout readable on small screens?
* Is the contrast acceptable?
* Have unnecessary colors, shadows, gradients, or decorative elements been avoided?

If any answer is no, revise the design before considering the task complete.
