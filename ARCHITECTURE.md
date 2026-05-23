# Architecture — MadAi.es

Website for **MadAi**, the Madrid AI Engineering tech community. It is a small, content-driven, bilingual (ES/EN) static site whose main job is to surface the next community event and link to the Lu.ma calendar where events are managed.

## Stack

| Concern | Choice |
|---|---|
| Static site generator | [Hugo](https://gohugo.io/) (`extended`), pinned to `0.148.2` for builds (Netlify), `0.161.0+extended` observed locally |
| Templating | Hugo's Go templates (`html/template`) |
| Markup | Markdown via Goldmark (`defaultMarkdownHandler = 'goldmark'`) |
| Styling | SCSS compiled by Hugo Pipes (`dartsass` transpiler, requires the `sass` binary in PATH) on top of vendored Bootstrap 5 |
| CSS framework | Bootstrap 5 SCSS sources, vendored in-repo under `vendor/bootstrap/` (not a package dependency) |
| Fonts | Google Fonts — Crimson Text (headings) + Open Sans (body), loaded via `<link>` in `site-style.html` (per DESIGN.md) |
| Hosting / CI | Netlify, auto-deploy from git; build command `hugo --minify` |
| External event platform | Lu.ma (embedded calendar + ticket links) |

There is **no Node/npm toolchain, no package manager, and no JS framework**. The only "dependency" is the Bootstrap SCSS source checked directly into `vendor/`.

## Build & deploy

- **Local dev:** `hugo server` (see `readme.md`).
- **Production build:** `hugo --minify` → output to `public/` (gitignored).
- **Netlify** (`netlify.toml`): pins `HUGO_VERSION = "0.148.2"`, publishes `public/`.
- SCSS pipeline (`layouts/partials/site-style.html`): uses the `dartsass` transpiler (Dart Sass, not the deprecated LibSass), so the build environment must have the `sass` binary available (locally via Homebrew; on Netlify set `HUGO_VERSION` to a Dart-Sass-bundled extended build or install `sass` in the build step). Our own SCSS is Dart-Sass-clean (module system, no deprecated globals); the only remaining deprecations originate **inside vendored Bootstrap 5.3.3** (slated to be fixed in Bootstrap 6, can't be touched per the no-edit rule on `vendor/`). Rather than silence specific deprecation IDs, Bootstrap is loaded as a **dependency**: `includePaths` adds `vendor/bootstrap` as a Sass load path (so partials `@use "bootstrap"` instead of a relative path), and `silenceDependencyDeprecations: true` suppresses warnings from dependency stylesheets only — our own code stays fully warned. In production, CSS is compiled `compressed`, fingerprinted, and served with a Subresource-Integrity hash; in dev it is `expanded` with source maps and no fingerprint. Production vs. dev is branched on `hugo.IsProduction`.

## Site configuration (`hugo.toml`)

- `baseURL = "https://madai.es"`.
- **Multilingual:** default language `es` (no subdir), secondary `en`. Both enabled. Language is selected per content file via the `*.es.md` / `*.en.md` filename suffix convention.
- Markup handler: Goldmark.
- **Output formats:** the home page emits `html` + a custom `llms` plain-text format (`[outputFormats.llms]`, `mediaType = text/plain`, `baseName = llms`) → renders `/llms.txt` (and `/en/llms.txt`) from `layouts/index.llms.txt`, a machine-readable site summary for LLM agents that auto-lists events.

## Content organization (`content/`)

Content drives everything; there is very little hardcoded copy in templates.

```
content/
  _index.{es,en}.md          # home page body (intro text)
  info/about.{es,en}.md      # "About us" page  → rendered by page.html
  events/                    # one file per language per event
    _index.{es,en}.md        # section index — activates /events and /en/events list routes
    202605-asier-marques.{es,en}.md
    202602-carlos-lopez.{es,en}.md
```

Each language variant is a sibling file with the matching `.es.md` / `.en.md` suffix. Hugo links them as translations of one logical page.

### Event front matter contract

Events live in section `events` and carry the data the home page and speaker page render. Key front-matter fields (TOML, `+++`):

- `layout = 'speaker-detail'`, `type = 'event'`
- `title`, `speaker`, `tagline`, `image` (speaker photo under `assets/img/speakers/`)
- `day`, `time`, `where` — event logistics shown on the event card
- `map` — Google Maps embed URL (rendered in an `<iframe>`)
- `ticketsUrl` — Lu.ma ticket link; when absent the CTA renders disabled (`join_cta_pending`)
- `date` — drives ordering; the home page selects the next event by `.ByDate`

Event bodies use two custom shortcodes (see below) plus freeform Markdown (e.g. an agenda table).

## Templating / layouts (`layouts/`)

```
layouts/
  index.html                  # home: intro + "next event" card
  _default/
    baseof.html               # HTML skeleton; defines header/main/footer blocks
    page.html                 # generic content page (renders Title + .Content)
    speaker-detail.html       # event/speaker detail page (back-to-events link + .Content)
  events/
    list.html                 # events list page: responsive card grid, sorted by date desc
  partials/
    site-style.html           # fonts + SCSS→CSS pipeline (+ "styles" block)
    site-meta.html            # per-page <title>/description, canonical, hreflang alternates, OG/Twitter cards
    schema.html               # JSON-LD structured data (Organization+WebSite on home, Event on event pages)
    site-header.html          # logo, About link, Events link, language switcher
    site-footer.html          # "give a talk" CTA (mailto) + manifesto (cached)
  _shortcodes/
    speaker_talk.html          # renders the talk title + description
    speaker_bio.html           # renders speaker photo (resized→webp) + bio
```

Notable template behavior:

- **`baseof.html`** composes the page from `header` / `main` / `footer` blocks, and includes `site-style`, `site-meta`, and `schema` partials in `<head>`. Footer uses `partialCached` since it's identical across pages.
- **`site-meta.html`** is SEO-aware: per-page `<title>` (`Page title | MadAI`, site title on home), `<meta description>` from the page's `description` front matter (falling back to the `meta_description` i18n string), `<link rel="canonical">`, and `<link rel="alternate" hreflang>` for each translation plus an `x-default` pointing at the home page.
- **`schema.html`** emits JSON-LD built as Go maps and serialized with `jsonify | safeJS` (the `safeJS` is required — `html/template` otherwise re-escapes `<script type="application/ld+json">` content as a JS string). Home pages get `Organization` + `WebSite`; event pages (`.Type == "event"`) get an `Event` whose `startDate` is parsed from the `day` (`DD.MM.YYYY`) + `time` front matter (not the `date` field), with `location` (Madrid), `performer` (speaker), `organizer` (MadAI), and a free `Offer` when `ticketsUrl` is set.
- **`index.html`** picks the next event with `first 1 (where site.RegularPages "Section" "events").ByDate` and renders its card (date/time/location, map iframe, Lu.ma CTA or disabled fallback).
- **`events/list.html`** renders all events sorted newest-first (`sort .RegularPages "Date" "desc"`) as a responsive Bootstrap grid of cards. Each card shows the speaker image (if present), date, talk title, speaker name, and tagline.
- **`speaker-detail.html`** includes a "back to events" link pointing to the events section index (`site.GetPage "/events"`).
- **`speaker_bio.html`** uses Hugo image processing: `resources.Get` + `.Resize` to emit a `webp q70 lanczos` image, rendered as a responsive `<img class="speaker-photo">` (full image, `width:100%`/`height:auto`, never cropped). The image path is passed via the `image` shortcode parameter.
- **Header navigation** (`site-header.html`) links Home / About / Events; each gets a `current` class for the active section (`.IsHome`, RelPermalink match for About, `Section == "events"` for Events), rendered as an underline indicator.
- **Language switcher** (`site-header.html`) iterates `Site.Home.AllTranslations`, marking the active language `current`. It is rendered as plain `ES` / `EN` text links (`.lang-switch`) styled to match the nav, not as pills. The events link is resolved via `site.GetPage "/events"` (Hugo resolves the permalink per active language).

## Internationalization

Three complementary mechanisms:

1. **UI strings** — `i18n/es.toml` and `i18n/en.toml`, looked up in templates via `{{ T "key" }}` (e.g. `latest_event`, `join_cta`, `meta_title`, `meta_description`, `home_h1`, `home_lead`, `talk`, `talk_title_pending`). `about_speaker` uses a `%s` placeholder filled with the speaker name. The home hero `h1` (`home_h1`, keyword-focused) and lead paragraph (`home_lead`) are kept separate from the `meta_description` so the page heading and the meta tag can be optimized independently.
2. **Page content** — per-language Markdown files (`*.es.md` / `*.en.md`).
3. **Manifesto block** — `assets/i18n/{lang}/manifesto.md` is loaded by the footer via `resources.Get` and `markdownify`, keyed on `.Lang`.

## Styling (`assets/scss/`)

- The SCSS uses the Sass **module system (`@use`/`@forward`), not `@import`** — required because `@import` is deprecated in Dart Sass. Entry point `assets/scss/main.scss` is an **orchestrator only**: it `@use`s `variables` (as `*`), then loads Bootstrap **with configuration** — `@use "bootstrap" with (...)` (resolved via the `vendor/bootstrap` load path set in `site-style.html`) passes the brand overrides (grayscale palette derived from the brand blue, `$primary`, `$link-color`, `$font-family-sans-serif`, `$headings-font-family`, rounded `$border-radius`, etc.) into Bootstrap's `!default` variables — then `@use`s the region partials (`base`, `header`, `hero`, `body-details`, `footer`, `events-list`). Bootstrap must be configured (the `with` load) before any partial `@use`s it unconfigured.
- Because `@use` is file-scoped, **each region partial declares its own `@use "variables" as *`** to access the MadAi tokens. `_header.scss` additionally `@use`s `sass:color` (for `color.adjust(..., $lightness: 6%)`, replacing the deprecated global `lighten()`) and `bootstrap as bs` (for `bs.media-breakpoint-down(sm)`).
- `_variables.scss` holds **only the DESIGN.md brand tokens** (calm/editorial, brand blue `#2a6f97`, soft `#e2eef2`, Crimson Text headings + Open Sans body, fonts, `$base-space`). The Bootstrap variable overrides are no longer kept here — they live in main.scss's `@use ... with (...)` configuration block.
- The region partials carry the project-specific rules. `_base.scss` has the element resets, link/button rules, focus ring, and the `a.back` link. `_header.scss` + `_hero.scss`: the **header + `#description` form one continuous brand-blue hero**: `header` (same-hue vertical gradient `light → primary`, faint white radial dot motif masked to fade in) carries white nav links with a `.current` underline indicator and a text-link language switcher (`.lang` / `.lang-switch`, `.current` = bright white + underline); `#description` continues the surface below (gradient `primary → primary-dark`, white `h1` + light accent rule, matching dot texture, `margin-top: -1px` to hide the seam). `_body-details.scss` holds `#body-details` / `#next-event` (event content + the `.event-info` card — date/location as an icon-chip list, an embedded map in a rounded frame, and a full-width primary CTA at the bottom; `next_event` eyebrow with a brand dot), the standalone `#next-event` agenda table, plus `#page`. The **header + `#description` form one continuous brand-blue hero**: `header` (same-hue vertical gradient `light → primary`, faint white radial dot motif masked to fade in) carries white nav links with a `.current` underline indicator and a text-link language switcher (`.lang` / `.lang-switch`, `.current` = bright white + underline); `#description` continues the surface below (gradient `primary → primary-dark`, white `h1` + light accent rule, matching dot texture, `margin-top: -1px` to hide the seam). Then `#body-details` / `#next-event` (event content + the `.event-info` card — date/location as an icon-chip list, an embedded map in a rounded frame, and a full-width primary CTA at the bottom; `next_event` eyebrow with a brand dot). The `.talk` block (eyebrow label via `> h2:not(.talk-title)` + sentence-case `.talk-title` on a fluid `clamp` scale) is defined directly under `#body-details` so the home (`#next-event`) and the speaker-detail page (`#page`) render the talk headline identically. Page titles share one fluid scale — `#page h1` and `#events-list h1` both use `clamp(2.2rem, 5vw, 3.4rem)`. Mobile-first: nav links are `white-space: nowrap` and a `media-breakpoint-down(sm)` block tightens nav/lang gaps so the header stays on one line on phones. The vertical rhythm is tuned for phones via `@media (max-width: 575.98px)` blocks in the region partials: the hero (`#description`) drops its desktop-scale top/bottom padding (and shrinks the clip), `#body-details` reduces its section padding plus the `.talk` / `.speaker-bio` / image margins, the footer trims its padding (together shrinking the trailing gap before the footer), and `#events-list .event-card` switches to `height:auto` (so single-column cards hug their content instead of stretching to equal grid heights) with a larger `margin-bottom` for separation. Bootstrap rows whose columns stack on phones but carry no trailing margin of their own (`.speaker-bio` photo→bio, the footer `.row` calendar-iframe→manifesto) get an explicit `row-gap: $base-space` in the same phone media query, so stacked columns separate as consistently as the rest of the vertical rhythm (~`$base-space` between major blocks). Links rendered as buttons (`.btn`) get an explicit `text-decoration:none` reset so they don't inherit the global underline. `#events-list` (cards with BEM selectors `.event-card`, pill date badges, image-zoom + title-recolor hover), footer (soft brand surface), and the agenda tables. The design favours **minimal separator lines** — heading underlines, the card `hr`, per-row table borders, the speaker-bio top rule, and footer top border were removed in favour of spacing and background contrast.
- Bootstrap 5 SCSS sources are vendored under `vendor/bootstrap/` (full source tree incl. mixins, helpers, forms, utilities). This is the framework dependency — vendored rather than pulled from npm.

## Static assets

- `assets/` — pipeline-processed assets (SCSS, images that get fingerprinted/resized, logo SVG, share image, speaker photos, i18n manifesto markdown).
- `static/` — copied verbatim to site root: favicons/PWA icons under `static/img/icon/`, `browserconfig.xml`, `site.webmanifest`, and `robots.txt` (allows all, points crawlers at `https://madai.es/sitemap.xml`).
- `resources/` — Hugo's generated asset cache (`_gen`), gitignored.

## Conventions & notes

- **Adding an event:** create the `*.es.md` / `*.en.md` pair under `content/events/` (filename `<YYYYMM>-<speaker-slug>`) following the front-matter contract above, fill the `speaker_talk` / `speaker_bio` shortcodes, and add a speaker image under `assets/img/speakers/`.
- **Calendar of record is Lu.ma** (`luma.com/madai`); the site embeds it and links tickets via `ticketsUrl`. Event registration is not handled on-site.
- `archetypes/default.md` is the scaffold for `hugo new` content (defaults `draft = true`).
- `public/` and `resources/_gen` are build output and are gitignored; `.hugo_build.lock` is a Hugo lockfile.
