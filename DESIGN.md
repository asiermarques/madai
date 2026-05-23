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

## Brand foundations

### Colors

The brand palette is implemented as SCSS tokens in `assets/scss/_variables.scss`. Use the token, never the raw hex.

| Token | Value | Role |
| --- | --- | --- |
| `$madai-primary` | `#2a6f97` | Main brand color: primary actions, links, accents, dark surfaces |
| `$madai-primary-dark` | `#235d80` | Hover/pressed states, gradient end-stops |
| `$madai-light` | `#e2eef2` | Soft supporting surface, pills, icon chips, hover fills |
| `$madai-white` | `#ffffff` | Cards and content surfaces |
| `$madai-bg` | `#ffffff` | Page background |
| `$madai-surface` | `#f7fafc` | Subtle alt surface (table headers, disabled states) |
| `$madai-border` | `#d7e3ea` | Hairline borders on cards, tables, media |
| `$madai-ink` | `#122a3a` | Headings and high-emphasis text |
| `$madai-text` | `#2c3e4a` | Body text |
| `$madai-muted` | `#5b7280` | Eyebrows, captions, metadata, secondary text |

Usage:

* Use `$madai-primary` as the main brand color. **Do not invent other blues** — derive shades from it (e.g. `color.adjust($madai-primary, $lightness: -6%)`) so everything stays on-brand.
* Reserve `$madai-light` for soft supporting surfaces and accents, never for primary actions.
* Prefer white / very light backgrounds when readability matters; use `$madai-primary` as the dark surface for hero and the talk CTA banner (white text on top).
* Only introduce new colors for semantic states (success/warning/error), not for decoration.

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

## Spacing & radius

Spacing is driven by a single token, `$base-space: 2rem`, multiplied for rhythm — e.g. `$base-space*0.75`, `$base-space*1.5`, `$base-space*1.75`. Reuse these multiples rather than introducing arbitrary `px` values, so vertical rhythm stays consistent across sections.

Corner radii follow a small implemented scale; pick the closest existing value rather than inventing new ones:

* `.375rem` — buttons, small back-links
* `.5rem` / `.55rem` — default cards, CTA buttons
* `.6rem` — icon chips, nested media (maps)
* `.75rem` — content cards, images, the talk CTA banner
* `.9rem` — the event-info card
* `999px` — pills (event date tags) and accent dots/dashes

**Responsive rhythm:** the phone breakpoint is `575.98px` (some components also use `767.98px`). On phones, tighten the desktop-scale vertical padding and add explicit `row-gap` to stacked columns — desktop spacing leaves dead gaps on small screens.

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

### Atmosphere is allowed — when it's brand-derived and subtle

"Avoid excessive gradients / decoration" does **not** mean flat solid colors everywhere. The implemented dark surfaces (hero, talk CTA banner) build quiet depth, and this is the intended house style:

* **Brand gradients only.** Two-tone gradients between `$madai-primary` and a slightly darker shade of itself (via `color.adjust`) — never multi-hue or rainbow gradients.
* **Subtle texture, masked.** A faint white dot-grid (`radial-gradient` dots at ~12–14% opacity) faded out with a diagonal `mask-image`. It should read as a whisper, not a pattern.
* **One quiet decorative anchor per surface.** e.g. an oversized `Crimson Text` glyph at ~7% opacity behind the CTA, or soft blurred gradient "orbs" in the hero. One, not several.
* **Restrained, brand-tinted shadows.** Lift shadows use `rgba($madai-primary / $madai-ink, …)`, not neutral black, and stay soft.

The line to hold: depth and atmosphere yes; noise, neon, heavy shadows, and competing decoration no.

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

Primary buttons: `$madai-primary` background, white text, `Open Sans` 600–700, radius `.375–.55rem` (slightly rounded, not pill). On hover, deepen to `$madai-primary-dark`, lift `translateY(-2px)`, and grow a soft brand-tinted shadow.

Inverted button (on a dark/brand surface, e.g. the CTA banner): white background, `$madai-primary` text. Pair with a `→` arrow that slides `translateX(4px)` on hover.

Secondary / utility (e.g. the back-link): transparent or white, `$madai-border`, muted text; on hover adopt `$madai-primary` border + `$madai-light` fill.

Disabled: `$madai-surface` background, `$madai-border`, `$madai-muted` text, no shadow, `cursor: not-allowed`.

Avoid: random accent colors, heavy shadows, oversized pills.

### Editorial eyebrow / kicker

A recurring signature: a short uppercase label above a heading.

* `Open Sans`, 700, `font-size` ~`.72–.8rem`, `letter-spacing` `.09em–.18em`, `text-transform: uppercase`.
* Color `$madai-muted` on light surfaces, `rgba(#fff,.85)` on brand surfaces.
* Accompanied by a small accent: a `999px` dot with a soft brand halo (`box-shadow: 0 0 0 4px rgba($madai-primary,.16)`), or a short `2px` dash before the text.

Use it for "Latest event", talk labels, the CTA kicker, and table headers — it carries the technical-but-editorial voice.

### Cards

Cards are white, with a `$madai-border` hairline and a radius from the scale (`.75rem` content cards, `.9rem` the event-info card). Shadows are optional, soft, and brand-tinted.

* **Event card (list):** image header + body; on hover lift `translateY(-4px)`, border turns `$madai-primary`, soft `rgba($madai-primary,.14)` shadow, image scales `1.03`, title turns brand color.
* **Event-info card:** icon-chip list (`$madai-light` rounded chips), bordered map, full-width primary CTA.
* **Talk CTA banner:** the one intentionally bold surface — brand gradient + masked dot-grid + faint serif glyph (see *Atmosphere* above). It lives **inside `.container`** (not full-bleed) so it keeps page margins on every breakpoint and aligns with the content around it.

Avoid heavy shadows, saturated/noisy backgrounds, and too many competing card styles.

### Tables (agenda style)

Agenda/schedule tables use a left brand rule (`border-left: 3px solid $madai-primary`), uppercase muted `th`, a first column treated as a timestamp/label (brand color, `tabular-nums`, fixed ~`5.5rem` width), and a hairline separator before the second column. Rows hover with a faint `rgba($madai-primary,.04)` tint. Empty header rows are hidden with `thead tr:has(th:empty) { display:none }`.

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

## Motion

Motion is calm and purposeful — it supports hierarchy, it doesn't decorate.

* **Page-load reveals:** staggered fade-up (`opacity` + `translateY`) on the hero headline and lead via `animation-delay` (e.g. `.3s`, `.45s`); the nav fades in from the top. One orchestrated entrance per page beats scattered micro-animations.
* **Ambient:** only the hero's blurred gradient orbs drift slowly (14–18s `ease-in-out infinite`). Keep ambient motion rare and slow.
* **Hover micro-interactions:** lift (`translateY(-2px…-4px)`), border/color shift to brand, soft brand-tinted shadow, the CTA arrow slide, the nav underline growing from the left. Durations `.15s–.22s`, `ease`.
* **Accessibility:** always respect `@media (prefers-reduced-motion: reduce)` and disable transitions/animations there. Keep visible `:focus-visible` outlines (2–3px brand or white).

---

## Implementation guidance

This is a **Hugo** site styled with **SCSS compiled through Bootstrap** — there is no Tailwind, no React, no CSS custom properties. Work within the existing token system:

* **Tokens live in `assets/scss/_variables.scss`** (`$madai-*` colors, `$base-space`, fonts). Reference these SCSS variables — never paste raw hex or arbitrary spacing.
* **Bootstrap is configured, not overridden after the fact.** `assets/scss/main.scss` passes brand tokens into Bootstrap via `@use "bootstrap" with (...)` (primary, link colors, radii, button settings, font families). Change Bootstrap behavior there, not by fighting it downstream.
* **Each UI region is its own partial** (`_hero.scss`, `_header.scss`, `_events-list.scss`, `_body-details.scss`, `_footer.scss`), wired up in `main.scss`. Add new components as a partial and `@use "variables" as *` at the top. Use BEM-style names (`block__element`).
* **Never edit `vendor/bootstrap/`.** All customization goes through `main.scss` / partials.
* **Derive shades in SCSS** with `@use "sass:color"` + `color.adjust(...)` rather than hardcoding new hex values.
* Requires **Hugo extended** (the SCSS pipeline depends on it). Fonts are already loaded — don't add font-loading code.

---

## Design review checklist

Before finishing any visual task, check:

* Are headings using `Crimson Text` and body/UI using `Open Sans`?
* Are you using `$madai-*` tokens and `$base-space` multiples — no raw hex or arbitrary `px`?
* Is `$madai-primary` the main brand color, with `$madai-light` only as soft support?
* Are gradients/textures brand-derived and subtle (not multi-hue, neon, or noisy)?
* Are radii from the implemented scale, not new values?
* Did you change Bootstrap config in `main.scss` rather than editing `vendor/`?
* Is the layout readable on small screens, with phone vertical rhythm tightened at `575.98px`?
* Is contrast acceptable, with visible focus states and `prefers-reduced-motion` respected?
* Is the design clean, calm, community-oriented — depth without noise?

If any answer is no, revise the design before considering the task complete.

## Credits

Logo design by [Ainara GM](https://www.ainaragm.es/)
