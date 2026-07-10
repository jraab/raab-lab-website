# Content & Landing Refresh — Design

**Date:** 2026-07-10
**Site:** Raab Lab (Quarto, `quarto/`), live at https://raab-lab.org
**Author:** Jesse Raab (with Claude Code)

## Goal

The migrated Quarto site is clean but bare — the landing page is a title plus two
sentences and two project cards, and the research content hasn't been updated in
years. This effort refreshes the front-facing content and gives the site a
stronger first impression, without touching the People / Publications mechanics.

Non-goals: group photo on the home page, a featured-publication home widget,
richer individual people profiles, a separate Join Us page. Parked for later.

## Scope — five coordinated pieces

### 1. Landing page (`quarto/index.qmd`, `quarto/styles/custom.scss`)

New top-to-bottom structure:

1. **Hero band** — full-width band using `quarto/images/raab-lab-header.jpg`
   (an H&E histology image, 1779×972). The image is light and busy, so the
   title "The Raab Lab" + tagline cannot sit directly over it in white. Use
   **one** of these, chosen on the live render:
   - a navy (`$primary`, `#13294B`) gradient scrim over the image with white
     title text, or
   - a split hero: image on one side, solid navy panel with the title on the other.
2. **Intro** — the existing two-sentence dept/mission text, kept (lightly
   refreshable).
3. **Recent highlights strip** — a horizontal row of the latest 3 News items
   (title · date · link), generated automatically from the `news/` listing.
4. **Research grid** — the two existing arm cards (Liver Cancer, Gene
   Regulation), unchanged in mechanism, now linking to the deeper project pages.

Visual tuning happens against a live `quarto preview` on localhost — iterate on
the real rendered site, not mockups.

### 2. News system (enables the section the migration left as a stub)

- **`quarto/news/`** — one dated post per item, `news/YYYY-MM-DD-slug/index.qmd`,
  frontmatter `title`, `date`, `description`, optional `image`.
- **`quarto/news.qmd`** — full reverse-chron listing (`type: default`,
  `sort: "date desc"`, `feed: true` for RSS).
- **Navbar** — add a `News` item in `quarto/_quarto.yml` (between Research and
  People, or after Publications — decided on render).
- **Home strip** — a second listing bound into `index.qmd` that shows the latest
  3 items from `news/`.
- **Seed content** — 3 starter posts so it doesn't launch empty:
  - Claire Olson joins the lab (grad student)
  - Sophia Shultz joins the lab (undergrad)
  - 1–2 recent papers (surfaced via `/focus`)
  - A clearly-labeled **template post** left in place for Jesse to fill in the
    recent award (added last, by Jesse, once the structure is visible).
  All seed blurbs pass through `/deslop`. Jesse approves/edits each before it ships.

### 3. Deeper project pages (`quarto/projects/*/index.qmd`)

Both arms — `gene-regulation/` and `liver-cancer/` — get:

- **Expanded narrative** — the current single thin paragraph grows into a few
  substantive paragraphs: the problem, the lab's approach, why it matters.
  Sourced via the content workflow below.
- **"Key Publications"** — a curated list of 3–6 standout papers for that arm,
  each linking to journal/DOI. Hand-picked via `/focus`. This is curation on the
  page, not a change to `references.bib`; the Publications page keeps the full
  list.
- `featured.jpg` retained (may be refreshed later, out of scope here).

### 4. Contact page (`quarto/contact.qmd`) — doubles as "Join Us"

- Add the lab photo (placeholder image until Jesse provides the real one).
- Lightly warm up the existing recruiting sentence for prospective
  students/postdocs/undergrads.
- No new page, no new nav item.

### 5. Content-production workflow

The engine for the project narratives, news blurbs, and paper picks:

1. **`/focus`** over the last ~5–6 years of publications → surfaces the standout
   papers and themes for each research arm.
2. Claude drafts each project narrative from those results plus any
   grant-proposal text Jesse pastes in.
3. Jesse edits / supplies grant passages — the drafts are his to own, nothing
   auto-generated ships without his pass.
4. **`/deslop`** conforms the drafts to Jesse's normal voice (news blurbs too).

## Sequencing — two phases

Split so the visual structure lands first and the slower content work can move
to a separate session if needed.

- **Phase 1 — Structure (this session).** Hero band, news system + navbar, home
  highlights strip, project pages restructured with placeholder narrative +
  empty "Key Publications" scaffolding, Contact lab-photo placeholder, all
  styling. Seed news posts may use short placeholder text. Goal: the site's new
  shape is fully renderable and previewable end-to-end, independent of the
  real research copy. Can be reviewed and deployed on its own.
- **Phase 2 — Content fill (this or a later session).** `/focus` over the pubs
  → draft project narratives → Jesse edits / pastes grant text → `/deslop`;
  curate the 3–6 Key Publications per arm; finalize real news blurbs; Jesse adds
  the award post. Phase 2 only edits content within the Phase 1 scaffolding, so
  it needs no structural rework and carries cleanly across sessions.

## Files touched

| File | Change |
|------|--------|
| `quarto/index.qmd` | Hero band, intro, news strip listing, research grid |
| `quarto/styles/custom.scss` | Hero styling (scrim/split), highlights strip, news card styles |
| `quarto/_quarto.yml` | Add `News` navbar item |
| `quarto/news.qmd` | New — full news listing + RSS |
| `quarto/news/*/index.qmd` | New — 3 seed posts + 1 award template |
| `quarto/projects/gene-regulation/index.qmd` | Expanded narrative + Key Publications |
| `quarto/projects/liver-cancer/index.qmd` | Expanded narrative + Key Publications |
| `quarto/contact.qmd` | Lab photo + refreshed recruiting text |
| `quarto/images/raab-lab-header.jpg` | New — hero image (move from repo root) |

## Assets needed from Jesse

- **Hero image** — provided: `raab-lab-header.jpg` (to be moved into `quarto/images/`).
- **Lab photo** for Contact — pending; placeholder until supplied.
- **Award details** — pending; Jesse adds the award news post last.
- **Grant text** (optional) — Jesse may paste passages to seed project narratives.

## Verification

- `quarto render` succeeds for all pages (no build errors).
- Home page shows hero with legible title, latest-3 news strip, two research cards.
- News page lists all seed posts newest-first; RSS feed generates.
- Each project page shows expanded narrative + Key Publications with working links.
- Contact page shows the (placeholder) lab photo and recruiting text.
- Live preview reviewed by Jesse before publishing.
- Deploy via `quarto publish gh-pages` only after Jesse approves the rendered site.
