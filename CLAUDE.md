# CLAUDE.md

Guidance for Claude when working in this repository.

**MadAi.es** is the bilingual (ES/EN) Hugo static site for the Madrid AI Engineering community. Read **[ARCHITECTURE.md](./ARCHITECTURE.md)** for the stack and **[DESIGN.md](./DESIGN.md)** for design rules and decisions, build/deploy, content model, and code organization — this file only adds working rules not covered there.

## Working rules

- **Keep ES/EN in sync.** Every content page is a `*.es.md` / `*.en.md` pair, and UI strings live in both `i18n/es.toml` and `i18n/en.toml`. When you add or change either, update both languages.
- **Customize styling through `assets/scss/main.scss`**, not the vendored Bootstrap. Don't edit files under `vendor/bootstrap/`.
- **Don't commit build output:** `public/` and `resources/_gen/` are gitignored; leave `.hugo_build.lock` alone.
- Requires **Hugo extended** (the SCSS pipeline depends on it).
- **Keep ARCHITECTURE.md up to date.** After any code change — new templates, layouts, i18n keys, content structure, shortcodes, or SCSS sections — update the relevant section of `ARCHITECTURE.md` to reflect the new state.
