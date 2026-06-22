---
name: add-event
description: Add a new MadAI event. Interviews the user for the required data, then scaffolds the bilingual (ES/EN) event content pair under content/events/ following the front-matter contract, the speaker_talk / speaker_bio shortcodes, and the agenda table. Use when the user wants to create, add, or publish a new event/talk/speaker on the MadAI site.
---

# Add a MadAI event

Your job is to create a new event on the MadAI site. An event is **a pair of Markdown files** — one per language (`*.es.md` / `*.en.md`) — under `content/events/`, plus a speaker photo under `assets/img/speakers/`.

Read `ARCHITECTURE.md` ("Event front matter contract" + "Adding an event") and `DESIGN.md` only if you need a refresher; everything you must produce is described below.

## Workflow

1. **Interview the user** for the required data (see checklist). Ask with `AskUserQuestion`, grouping related fields, and offer sensible defaults/options. Skip anything the user already provided in their request. Keep ES/EN in mind — ask for both-language copy where it differs.
2. **Confirm the file slug and date** before writing.
3. **Create the speaker photo reference.** Ask where the photo is; copy/place it under `assets/img/speakers/<slug>.<ext>` (jpg/jpeg/png). If the user has no photo yet, note it and use the expected path anyway.
4. **Write both files** (`.es.md` and `.en.md`) following the templates below. Keep ES/EN in sync — every field and section must exist in both, translated.
5. **Verify the build** with `hugo` (or `hugo server` briefly) if available, and report the new event's URL paths (`/events/<slug>/` and `/en/events/<slug>/`).

## Required data (interview checklist)

Gather all of these. Ask in a few grouped `AskUserQuestion` rounds rather than one giant list.

**Identity & scheduling**
- **Speaker name** (e.g. `Asier Marqués`).
- **Speaker slug** — lowercase, hyphenated, no accents (e.g. `asier-marques`). Derive a suggestion from the name and confirm.
- **Event year + month** → builds the `YYYYMM` filename prefix; the full file slug is `<YYYYMM>-<speaker-slug>` (e.g. `202605-asier-marques`).
- **`day`** — event date as `DD.MM.YYYY` (this drives the JSON-LD `startDate`, parsed together with `time`).
- **`time`** — start time as `HH:MMh` (e.g. `19:00h`).
- **`date`** — TOML datetime used for ordering / "next event" selection (e.g. `2026-05-21T19:00:00Z`). Default it to the `day` + `time` if the user doesn't specify.

**Talk**
- **Talk title** — in ES and EN. Goes into `speaker_talk title="..."`.
- **`title`** front-matter field — the canonical event title (the EN talk title is typically used in both files; confirm with the user).
- **Talk description** — the abstract, in ES and EN (the body of the `speaker_talk` shortcode).

**Speaker**
- **`tagline`** — speaker role/company, in ES and EN (e.g. `Engineering Manager at BestSecret Group`).
- **Speaker bio** — short bio, in ES and EN (body of `speaker_bio`).
- **Speaker links** — LinkedIn / Bluesky / X / web, as a Markdown bullet list inside the bio.
- **Photo** — path to the speaker image to place under `assets/img/speakers/`.

**Logistics**
- **`where`** — venue, e.g. `Puerta de innovación, Madrid`.
- **`map`** — Google Maps embed URL (the `https://www.google.com/maps/embed?pb=...` form). Ask the user to paste it.
- **`ticketsUrl`** — Lu.ma ticket link (e.g. `https://luma.com/xxxxxxx`). **If the user doesn't have it yet, omit the field** — the CTA renders disabled automatically.
- **`description`** front-matter field — short SEO description per language (e.g. `Asier Marqués en MadAI - <tagline>` / `... at MadAI - <tagline>`).

**Agenda**
- Confirm the schedule rows (welcome / talk / networking / closing) and times. Offer the standard agenda as a default and let the user tweak it.

## File templates

`content/events/<slug>.es.md`:

```md
+++
date = <DATE>
draft = false
layout = 'speaker-detail'
type = 'event'
title = '<TITLE>'
speaker = '<SPEAKER NAME>'
tagline = '<TAGLINE ES>'
day = '<DD.MM.YYYY>'
time = '<HH:MMh>'
where = '<VENUE>'
map = '<GOOGLE MAPS EMBED URL>'
ticketsUrl = '<LUMA URL>'        # omit this line entirely if not available yet
description = '<SPEAKER> en MadAI - <TAGLINE ES>'
image = 'img/speakers/<slug>.<ext>'
+++

{{<speaker_talk title="<TALK TITLE ES>">}}
<TALK DESCRIPTION ES>
{{</speaker_talk>}}

{{<speaker_bio title="<SPEAKER NAME>" tagline="<TAGLINE ES>" image="img/speakers/<slug>.<ext>">}}
<BIO ES>

* [Linkedin](<URL>)
* [Bluesky](<URL>)
{{</speaker_bio>}}

##### Agenda

|       |                            |
|-------|----------------------------|
| 18:30 | Bienvenida y recepción     |
| 19:00 | Charla: <TALK TITLE ES>    |
| 20:00 | Networking                 |
| 20:30 | Cierre                     |
```

`content/events/<slug>.en.md` — same front matter (translate `tagline` and `description`, use `... at MadAI - <TAGLINE EN>`), with the body translated:

```md
{{<speaker_talk title="<TALK TITLE EN>">}}
<TALK DESCRIPTION EN>
{{</speaker_talk>}}

{{<speaker_bio title="<SPEAKER NAME>" tagline="<TAGLINE EN>" image="img/speakers/<slug>.<ext>">}}
<BIO EN>

* [Linkedin](<URL>)
* [Bluesky](<URL>)
{{</speaker_bio>}}

##### Agenda

|       |                          |
|-------|--------------------------|
| 18:30 | Welcome                  |
| 19:00 | Talk: <TALK TITLE EN>    |
| 20:00 | Networking               |
| 20:30 | Closing                  |
```

## Rules

- **Keep ES/EN in sync** — both files must exist with matching front-matter fields and equivalent, translated content (CLAUDE.md working rule).
- **Filename convention:** `<YYYYMM>-<speaker-slug>`, slug lowercase/hyphenated/no accents.
- **`day`/`time` drive the schema `startDate`; `date` drives ordering** — set both consistently.
- **Omit `ticketsUrl` when unknown** instead of leaving it empty — the disabled CTA fallback depends on its absence.
- Use TOML front matter delimited by `+++`.
- Don't edit `vendor/`, `public/`, or `resources/`. After creating the event, **update `ARCHITECTURE.md` only if the content model changed** (a normal new event does not change it).
- Place the speaker photo under `assets/img/speakers/`; the `image` path is relative to `assets/` (`img/speakers/<slug>.<ext>`).
