# Content & Landing Refresh Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Refresh the Raab Lab Quarto site's landing page and research content — a hero band, a News system feeding a home highlights strip, deeper project pages, and a Contact page that doubles as "join us."

**Architecture:** Two phases. Phase 1 builds all *structure* (hero, news system, restructured project pages, contact) with placeholder/real-but-simple copy so the site's new shape renders end-to-end and can be previewed and deployed on its own. Phase 2 fills the *research content* using the `/focus` (mine publications) and `/deslop` (voice) skills, editing only within Phase 1's scaffolding so it carries cleanly to a separate session.

**Tech Stack:** Quarto 1.9.x (website project), Bootstrap `flatly` theme + `styles/custom.scss`, Quarto listings for News and project grids, native `title-block-banner` for the hero.

## Global Constraints

- All site files live under `quarto/`. Run every `quarto` command from `quarto/`.
- Do NOT modify `references.bib`, the People YAML files, `_templates/people-card.ejs`, or `chicago-author-date.csl` — out of scope.
- Palette (from `styles/custom.scss`): `$primary` = `#13294B` (UNC navy), `$link-color` = `#2C7A7B` (teal), `$body-bg` = `#f7f9fb`.
- "Test" = `quarto render <file>` exits 0 AND a `grep` of the output HTML in `_site/` finds the expected marker. `_site/` and `.quarto/` are git-ignored — never commit them.
- Commit source only (`.qmd`, `.yml`, `.scss`, images). Do not push; the user pushes/publishes.
- Verify each render from `quarto/` as the working directory.

---

# PHASE 1 — Structure (this session)

### Task 1: Vendor the hero image into the site

**Files:**
- Create: `quarto/images/raab-lab-header.jpg` (moved from repo-root `raab-lab-header.jpg`)

**Interfaces:**
- Produces: hero image at site path `images/raab-lab-header.jpg`, consumed by Task 3.

- [ ] **Step 1: Move the file into the site image tree**

```bash
git mv raab-lab-header.jpg quarto/images/raab-lab-header.jpg
```

- [ ] **Step 2: Verify it landed and is a valid JPEG**

```bash
file quarto/images/raab-lab-header.jpg
```
Expected: `JPEG image data ... 1779x972`

- [ ] **Step 3: Commit**

```bash
git add -A && git commit -m "Add hero image to quarto/images/"
```

---

### Task 2: News system (listing page, navbar, seed posts)

**Files:**
- Create: `quarto/news.qmd`
- Create: `quarto/news/2026-07-01-claire-olson/index.qmd`
- Create: `quarto/news/2026-06-15-sophia-shultz/index.qmd`
- Create: `quarto/news/2026-01-01-award-TEMPLATE/index.qmd`
- Modify: `quarto/_quarto.yml` (add News navbar item)

**Interfaces:**
- Produces: a `news/` listing directory that Task 3's home strip reads via `contents: news`. Each post has frontmatter `title` (string), `date` (`YYYY-MM-DD`), `description` (string).

- [ ] **Step 1: Create the News listing page**

Create `quarto/news.qmd`:

```markdown
---
title: "News"
listing:
  contents: news
  type: default
  sort: "date desc"
  fields: [date, title, description]
  feed: true
---
```

- [ ] **Step 2: Create the first seed post (Claire)**

Create `quarto/news/2026-07-01-claire-olson/index.qmd`:

```markdown
---
title: "Claire Olson joins the lab"
date: "2026-07-01"
description: "Claire Olson joins the Raab Lab as a graduate student."
---

We're glad to welcome Claire Olson, who joins the lab as a graduate student.
```

- [ ] **Step 3: Create the second seed post (Sophia)**

Create `quarto/news/2026-06-15-sophia-shultz/index.qmd`:

```markdown
---
title: "Sophia Shultz joins the lab"
date: "2026-06-15"
description: "Sophia Shultz joins the Raab Lab as an undergraduate researcher."
---

We're glad to welcome Sophia Shultz, who joins the lab as an undergraduate researcher.
```

- [ ] **Step 4: Create the award template post (for Jesse to fill last)**

Create `quarto/news/2026-01-01-award-TEMPLATE/index.qmd`:

```markdown
---
title: "TEMPLATE — replace with the award announcement"
date: "2026-01-01"
description: "One-line summary shown on the home strip and news page."
draft: true
---

<!--
TEMPLATE for the recent award. To publish:
1. Rename this folder to news/YYYY-MM-DD-<slug>/ using the real date.
2. Set title, date, and description above.
3. Remove `draft: true`.
4. Write the announcement below.
-->

Describe the award here.
```

Note: `draft: true` keeps the template out of the rendered listing until Jesse is ready.

- [ ] **Step 5: Add News to the navbar**

In `quarto/_quarto.yml`, modify the `navbar.left` list to insert News after Publications:

```yaml
    left:
      - text: "Research"
        href: index.qmd#research
      - text: "People"
        href: people.qmd
      - text: "Publications"
        href: publications.qmd
      - text: "News"
        href: news.qmd
      - text: "Contact"
        href: contact.qmd
```

- [ ] **Step 6: Render and verify the news page lists the two real posts (not the draft)**

```bash
cd quarto && quarto render news.qmd 2>&1 | tail -3
grep -c -E "Claire Olson joins the lab|Sophia Shultz joins the lab" _site/news.html
grep -c "TEMPLATE — replace" _site/news.html
```
Expected: render succeeds; first grep = `2`; second grep = `0` (draft excluded).

- [ ] **Step 7: Commit**

```bash
git add quarto/news.qmd quarto/news quarto/_quarto.yml
git commit -m "Add News section with seed posts and navbar item"
```

---

### Task 3: Landing page — hero band, intro, news strip, research grid

**Files:**
- Modify: `quarto/index.qmd` (full rewrite of frontmatter + body)
- Modify: `quarto/styles/custom.scss` (append hero + news-strip rules)

**Interfaces:**
- Consumes: `images/raab-lab-header.jpg` (Task 1), `news/` listing (Task 2).
- Produces: home page with a `#news-strip` listing (latest 3) and the existing `#projects` grid.

- [ ] **Step 1: Rewrite `quarto/index.qmd`**

Replace the entire file with:

```markdown
---
title: "The Raab Lab"
subtitle: "Chromatin-mediated gene regulation in cancer"
title-block-banner: images/raab-lab-header.jpg
title-block-banner-color: "#ffffff"
listing:
  - id: news-strip
    contents: news
    type: grid
    grid-columns: 3
    max-items: 3
    sort: "date desc"
    fields: [date, title, description]
  - id: projects
    contents: "projects/*/index.qmd"
    type: grid
    grid-columns: 2
    image-height: 220px
    fields: [image, title, description]
    sort: false
---

We are in the Department of Genetics at UNC Chapel Hill. We study how
chromatin-mediated gene regulation is controlled and how disruption of chromatin
contributes to cancer and other diseases.

## Recent News {#news}

:::{#news-strip}
:::

## Research {#research}

:::{#projects}
:::
```

- [ ] **Step 2: Append hero + news-strip styling to `quarto/styles/custom.scss`**

Append to the end of the `/*-- scss:rules --*/` section:

```scss
// Hero banner: navy scrim over the (light, busy) histology image so white title reads
.quarto-title-banner {
  background-image:
    linear-gradient(rgba(19, 41, 75, .62), rgba(19, 41, 75, .72)),
    url(../images/raab-lab-header.jpg);
  background-size: cover;
  background-position: center;
  padding-top: 3.5rem;
  padding-bottom: 3.5rem;
}
.quarto-title-banner .title { font-size: 3rem; }
.quarto-title-banner .subtitle {
  color: rgba(255, 255, 255, .9);
  font-size: 1.35rem;
}

// Recent News strip: compact cards, date accent
#news-strip .card { min-height: 100%; }
#news-strip .listing-date {
  color: $link-color;
  font-weight: 600;
  font-size: .85rem;
}
#news-strip .card-title { font-size: 1.05rem; margin-top: .2rem; }
```

- [ ] **Step 3: Render the home page and verify hero + news strip + projects all present**

```bash
cd quarto && quarto render index.qmd 2>&1 | tail -3
grep -c "quarto-title-banner" _site/index.html
grep -c -E "Claire Olson joins the lab|Sophia Shultz joins the lab" _site/index.html
grep -c -E "Gene Regulation|Chromatin Disruption in Liver Cancer" _site/index.html
```
Expected: render succeeds; banner grep ≥ `1`; news grep = `2`; projects grep ≥ `2`.

- [ ] **Step 4: Commit**

```bash
git add quarto/index.qmd quarto/styles/custom.scss
git commit -m "Rebuild landing page: hero banner, news strip, research grid"
```

---

### Task 4: Deeper project pages — narrative scaffold + Key Publications section

**Files:**
- Modify: `quarto/projects/gene-regulation/index.qmd`
- Modify: `quarto/projects/liver-cancer/index.qmd`

**Interfaces:**
- Produces: each project page with an expanded (placeholder) narrative and a `## Key Publications` section that Phase 2 fills. Frontmatter (`title`, `description`, `image`) is preserved verbatim so the home grid keeps working.

- [ ] **Step 1: Restructure `quarto/projects/gene-regulation/index.qmd`**

Keep the existing frontmatter unchanged. Replace the body with:

```markdown
Chromatin remodelers are large, multi-subunit complexes that can be assembled in
diverse configurations. We use genome-wide approaches to understand how these
complexes locate their genomic targets, what they do once they arrive, and how
their assembly is regulated. Our current focus is on the roles of
post-translational modifications of non-histone proteins and RNA interactions as
key mediators of complex assembly and function.

<!-- PHASE 2: expand the narrative above (problem → approach → why it matters)
     using /focus results and any grant text, then /deslop for voice. -->

## Key Publications

<!-- PHASE 2: replace with 3–6 curated papers for this arm, e.g.
- Author A, Author B. *Title*. Journal. Year. [link](https://doi.org/...)
-->

_Selected publications for this project will appear here._
```

- [ ] **Step 2: Restructure `quarto/projects/liver-cancer/index.qmd`**

Keep the existing frontmatter unchanged. Replace the body with:

```markdown
Chromatin regulators are among the most frequently mutated genes in
hepatocellular carcinoma and cholangiocarcinoma. We are interested in how
disruption of normal chromatin regulation drives the development of these
tumors. We are also exploring new therapeutic strategies targeting the
epigenome, using high-throughput CRISPR screens to uncover novel therapeutic
avenues.

<!-- PHASE 2: expand the narrative above (problem → approach → why it matters)
     using /focus results and any grant text, then /deslop for voice. -->

## Key Publications

<!-- PHASE 2: replace with 3–6 curated papers for this arm, e.g.
- Author A, Author B. *Title*. Journal. Year. [link](https://doi.org/...)
-->

_Selected publications for this project will appear here._
```

- [ ] **Step 3: Render both project pages and verify the Key Publications heading exists**

```bash
cd quarto && quarto render projects/gene-regulation/index.qmd 2>&1 | tail -2
quarto render projects/liver-cancer/index.qmd 2>&1 | tail -2
grep -rc "Key Publications" _site/projects/*/index.html
```
Expected: both render; each file grep = `1`.

- [ ] **Step 4: Commit**

```bash
git add quarto/projects/gene-regulation/index.qmd quarto/projects/liver-cancer/index.qmd
git commit -m "Scaffold deeper project pages with Key Publications sections"
```

---

### Task 5: Contact page — lab photo placeholder + warmer recruiting text

**Files:**
- Modify: `quarto/contact.qmd`

**Interfaces:**
- Produces: a Contact page carrying a (commented-out) lab-photo slot and refreshed recruiting copy. No new nav item.

- [ ] **Step 1: Update `quarto/contact.qmd`**

Replace the file with:

```markdown
---
title: "Contact"
---

<!-- LAB PHOTO: when Jesse provides the photo, drop it in quarto/images/lab-photo.jpg
     and uncomment the line below.
![The Raab Lab](images/lab-photo.jpg){.img-fluid style="border-radius:.6rem;margin-bottom:1.5rem;"}
-->

We are located in the Department of Genetics at the University of North Carolina
at Chapel Hill.

We are always glad to hear from people who want to join us. Prospective graduate
students, postdocs, and undergraduate researchers are all encouraged to get in
touch — tell us a bit about what you're excited about and what you'd like to work on.

**Email:** [jraab@med.unc.edu](mailto:jraab@med.unc.edu)

**Address:**\
Department of Genetics\
120 Mason Farm Road\
Chapel Hill, NC 27516\
United States
```

- [ ] **Step 2: Render and verify the recruiting text renders and the image slot stays commented (no broken image)**

```bash
cd quarto && quarto render contact.qmd 2>&1 | tail -2
grep -c "want to join us" _site/contact.html
grep -c "lab-photo.jpg" _site/contact.html
```
Expected: render succeeds; first grep = `1`; second grep = `0` (image line still in an HTML comment, not emitted as an `<img>`).

- [ ] **Step 3: Commit**

```bash
git add quarto/contact.qmd
git commit -m "Refresh Contact page: recruiting text + lab photo slot"
```

---

### Task 6: Phase 1 full-site verification + preview gate

**Files:** none (verification only)

- [ ] **Step 1: Full render of the whole site**

```bash
cd quarto && quarto render 2>&1 | tail -8
```
Expected: all pages render, ending `Output created: _site/index.html`, no errors.

- [ ] **Step 2: Start a live preview for visual review**

```bash
cd quarto && quarto preview --no-browser --port 4321
```
Then open `http://localhost:4321/`. Confirm: hero title is legible over the scrim; the news strip shows Claire + Sophia; the two research cards link to the deeper pages; News is in the navbar; Contact reads well.

- [ ] **Step 3: STOP for user review**

Phase 1 is a natural checkpoint. Present the preview to Jesse. Decide the hero treatment (scrim as built vs. switch to a split layout) and navbar position for News against the real render before proceeding. Do not deploy until Jesse approves.

---

# PHASE 2 — Research content (this or a later session)

> Phase 2 edits only content inside Phase 1's scaffolding. It uses the `/focus`
> skill (mine ~5–6 years of publications for standout papers + themes) and
> `/deslop` (conform drafts to Jesse's voice). Each task ends in a render check
> and commit. These tasks are interactive — they depend on `/focus` output and
> Jesse's edits/grant text — so treat the code blocks as the target shape, not
> fixed copy.

### Task 7: Mine publications with /focus

**Files:** none (produces notes used by Tasks 8–10)

- [ ] **Step 1:** Invoke `/focus` over `quarto/references.bib`, scoped to the last ~5–6 years, to surface the standout papers and group them by research arm (gene regulation vs. liver cancer).
- [ ] **Step 2:** Record the shortlist per arm (3–6 papers each) with title, journal, year, and DOI/link. Hand the shortlist to Jesse to confirm/adjust the picks.

### Task 8: Draft and voice the project narratives

**Files:**
- Modify: `quarto/projects/gene-regulation/index.qmd`
- Modify: `quarto/projects/liver-cancer/index.qmd`

- [ ] **Step 1:** For each arm, draft an expanded narrative (problem → approach → why it matters) from the `/focus` themes plus any grant text Jesse pastes in, replacing the placeholder paragraph and the `PHASE 2` narrative comment.
- [ ] **Step 2:** Run `/deslop` on each narrative to match Jesse's voice. Jesse edits.
- [ ] **Step 3:** Render both pages (`quarto render projects/*/index.qmd`) — expect success. Commit: `git commit -m "Write project narratives (gene regulation, liver cancer)"`.

### Task 9: Fill Key Publications per arm

**Files:**
- Modify: `quarto/projects/gene-regulation/index.qmd`
- Modify: `quarto/projects/liver-cancer/index.qmd`

- [ ] **Step 1:** Replace each `## Key Publications` placeholder with the confirmed 3–6 papers as a markdown list, each linking to its DOI, e.g. `- Author A, Author B. *Title*. Journal. Year. [link](https://doi.org/...)`.
- [ ] **Step 2:** Render both pages; verify each links resolves in preview. Commit: `git commit -m "Add curated Key Publications to project pages"`.

### Task 10: Finalize news blurbs + award post

**Files:**
- Modify: `quarto/news/2026-07-01-claire-olson/index.qmd`, `quarto/news/2026-06-15-sophia-shultz/index.qmd`
- Create: `quarto/news/<date>-<slug>/index.qmd` (recent paper highlight(s), optional)
- Modify: `quarto/news/2026-01-01-award-TEMPLATE/index.qmd` → real award post (Jesse-led)

- [ ] **Step 1:** Optionally add 1–2 recent-paper highlight posts drawn from the `/focus` shortlist; `/deslop` the blurbs.
- [ ] **Step 2:** Jesse fills the award template: rename the folder to the real date/slug, set frontmatter, remove `draft: true`, write the announcement.
- [ ] **Step 3:** `quarto render news.qmd`; confirm all intended posts appear newest-first, template no longer present. Commit: `git commit -m "Finalize news posts and award announcement"`.

### Task 11: Final render, preview, deploy

- [ ] **Step 1:** `cd quarto && quarto render` — all pages succeed.
- [ ] **Step 2:** `quarto preview` — Jesse reviews the fully-populated site.
- [ ] **Step 3:** On approval: `git push origin main` (source) and `quarto publish gh-pages` (deploy). Confirm the live site updated.

---

## Notes for the implementer

- Quarto's `title-block-banner` with an image path renders the frontmatter
  title over that image as a background; the scrim in Task 3 Step 2 is a
  navy gradient layered over it in SCSS so white text stays legible. The SCSS
  `url(../images/...)` path is relative to the compiled CSS in `_site/site_libs/`,
  which is why it steps up one directory — verify in preview and adjust if the
  image 404s.
- If, on review, Jesse prefers a split hero (image beside a navy title panel)
  over the scrim, that's a Task 3 revision: drop `title-block-banner`, and build
  a two-column raw-HTML hero block at the top of `index.qmd` with matching SCSS.
- Draft posts (`draft: true`) are excluded from listings and the RSS feed but
  still exist as files — the award template stays invisible until Jesse ships it.
