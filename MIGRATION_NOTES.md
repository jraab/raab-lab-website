# Migration Notes — Hugo/Wowchemy → Quarto

This repository's lab website was migrated from Hugo + the Wowchemy/HugoBlox
Academic theme to [Quarto](https://quarto.org). The new site lives in the
`quarto/` subdirectory. **The old Hugo site is untouched** — all original files
(`content/`, `config/`, `assets/`, `static/`, `layouts`, etc.) remain in place
so you can compare or roll back until you delete them.

---

## What changed

| Area | Old (Hugo/Wowchemy) | New (Quarto) |
|------|---------------------|--------------|
| Build tool | Hugo `0.119.0` + Wowchemy modules | Quarto (bundled with RStudio/Positron) |
| Home | `content/_index.md` (widget blocks) | `quarto/index.qmd` (hero + project grid) |
| People | `content/authors/<name>/` + People widget | `quarto/people.qmd` + `quarto/people/*.yml` (grid) |
| Publications | `content/publication/<slug>/` (per-paper frontmatter) | `quarto/publications.qmd` rendered from `quarto/references.bib` via CSL |
| Projects | `content/projects/<slug>/` | `quarto/projects/<slug>/index.qmd` |
| Contact | `content/contact/index.md` | `quarto/contact.qmd` |
| Research / News | placeholder / empty | dropped (see "News" below) |
| Theme | Wowchemy theme | `flatly` Bootswatch base + `quarto/styles/custom.scss` |

**Structural template:** the layout follows the
[Masiello Group](https://github.com/MasielloGroup/MasielloGroupWebsite) Quarto
site (itself a Wowchemy→Quarto migration), simplified. People use the single-page
grid pattern from the [Gang He template](https://github.com/drganghe/quarto-academic-website-template).

---

## Editing the site (your routine updates)

You edit Markdown/YAML files and re-render. No code.

### Add a publication
Publications are driven entirely by **`quarto/references.bib`** — a single
BibTeX file (consolidated from the 21 old per-paper `cite.bib` files). To add a
paper, paste its BibTeX entry into `references.bib` and re-render. It appears
automatically on the Publications page, sorted newest-first. **Do not** create
per-paper pages or duplicate metadata in frontmatter.

- Formatting is controlled by `quarto/chicago-author-date.csl` (a standard
  Chicago author-date style, modified only to sort the bibliography by date
  descending). Swap this file to change citation style.
- The page uses Quarto's native `nocite: "@*"` to list every entry in the `.bib`.

### Add or move a person
Each role group is one YAML file in `quarto/people/` (`pi.yml`, `postdocs.yml`,
`grad-students.yml`, `staff.yml`, `undergrads.yml`, `alumni.yml`). Add an entry:

```yaml
- title: Jane Doe
  subtitle: "Graduate Student"
  image: /images/people/jane-doe.jpg
  email: jane@email.unc.edu          # all social fields are optional
  scholar: https://scholar.google.com/citations?user=...
  github: https://github.com/janedoe
  # also supported: orcid, twitter, bluesky, website
```

Each social field you add renders as an icon under the person's name. Supported
fields: `email`, `scholar`, `orcid`, `github`, `twitter`, `bluesky`, `website`.
Omit any you don't have. Drop the photo in `quarto/images/people/`. To move
someone to alumni, cut their block from one file and paste it into `alumni.yml`.

The icon row is drawn by the custom listing template
`quarto/_templates/people-card.ejs`; you won't normally need to touch it.

### Add a project
Create `quarto/projects/<slug>/index.qmd` with `title`, `description`, and
`image: featured.jpg`, plus a `featured.jpg` in the same folder. It appears in
the homepage Research grid automatically.

### News / blog (not currently enabled)
The old `/post/` section was empty, so no news page ships. To enable one later:
1. Create `quarto/news/` with one folder per post: `news/<date>-<slug>/index.qmd`
   (frontmatter: `title`, `date`, `description`).
2. Create `quarto/news.qmd`:
   ```yaml
   ---
   title: "News"
   listing:
     contents: news
     type: default
     sort: "date desc"
     feed: true
   ---
   ```
3. Add `- text: "News"` / `href: news.qmd` to the navbar in `_quarto.yml`.

---

## Building and previewing locally

Quarto ships inside RStudio and Positron, so you already have it. From the
`quarto/` directory:

```bash
quarto preview     # live-reloading local preview
quarto render      # one-off build into quarto/_site/
```

If `quarto` isn't on your shell PATH, use the bundled binary or open the project
in RStudio/Positron and use the Render button. The bundled binary on this
machine is:
`/Applications/RStudio.app/Contents/Resources/app/quarto/bin/quarto`
(Quarto `1.8.25`).

`quarto/_site/` and `quarto/.quarto/` are build output and are git-ignored.

---

## Deployment (GitHub Pages, fronted by Cloudflare)

Netlify is no longer used. The site is published to **GitHub Pages** from your
machine with one command, and your custom domain is served through Cloudflare
DNS. There is **no build config file and no CI** — `quarto publish gh-pages`
renders locally and pushes the built site to a `gh-pages` branch.

> The repo's `netlify.toml` is the **old Hugo** config, left untouched with the
> rest of the old site. Delete it whenever you remove the Hugo files.

### One-time setup
1. **Domain is set to `raab-lab.org`** in two places (already done):
   - `site-url` in `quarto/_quarto.yml` — used for the sitemap and social-card URLs.
   - `quarto/CNAME` — a one-line file with the domain. It's listed under
     `resources` in `_quarto.yml` so it's copied into every build and keeps
     GitHub Pages bound to the domain across publishes.
2. **First publish** (from the `quarto/` directory):
   ```bash
   quarto publish gh-pages
   ```
   This creates the `gh-pages` branch, adds `.nojekyll` automatically, and pushes
   the rendered site. Answer "yes" when it asks to publish.
3. **In GitHub repo settings → Pages** (repo `jraab/raab-lab-website`): confirm
   the source is the `gh-pages` branch and the custom domain shows `raab-lab.org`
   (the `CNAME` file sets this). Leave "Enforce HTTPS" on.
4. **In Cloudflare DNS** for `raab-lab.org` (an apex/root domain), point it at
   GitHub Pages with **either**:
   - four `A` records → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`,
     `185.199.111.153` (add the matching `AAAA` records for IPv6 if you like), **or**
   - a `CNAME` at the root → `jraab.github.io` (Cloudflare flattens root CNAMEs).

   Cloudflare's proxy (orange cloud) can stay on; set SSL/TLS mode to **Full**.
   GitHub Pages serves your apex domain even though this is a project repo (not
   `jraab.github.io`) — the `CNAME` file is what makes that work.

### Routine deploys
After editing content, from `quarto/`:
```bash
quarto publish gh-pages     # renders + deploys in one step
```
Optionally also commit your **source** to `main` for history/backup
(`git add -A && git commit && git push`). Note: `main` holds the source; the
`gh-pages` branch holds the built site — keep them separate and never hand-edit
`gh-pages`.

## Old URLs from the Wowchemy site

We chose **not** to add redirects for old Wowchemy URLs (e.g.
`/publication/<slug>/`). GitHub Pages can't do server-side redirects, the
exposure is limited to deep links (the homepage and main nav keep working), and
search engines re-crawl to the new URLs within a few weeks. If you ever want to
preserve a specific old link, add a **Redirect Rule in Cloudflare** — no site
change needed.

---

## Notes, assumptions, and things to verify

- **Dropped Wowchemy-specific fields** during migration: widget blocks,
  `user_groups`, `superuser`, `highlight_name`, education stubs, and `weight`.
  Social links were re-implemented as icon rows on the people cards (see "Add or
  move a person"). The PI's email/Scholar/GitHub/X links from the old profile are
  populated; other members currently show email only — add more as people have them.
- **People composition** was read from the old `user_groups`: PI (Jesse Raab),
  Postdoc (Margarita Dzama), Grad Students (Jackie Brinkman, Mallory Sokolowski),
  Staff (Peyton Kuhlers), Undergrad (Julia Boltz), Alumni (Carl Manner, Deena
  Scoville, Ian Sturgill, Lindsey Hernandez, Max Bucklan). Verify this is current.
- **No individual people pages** (per your choice of the single-grid design).
- **Images** were copied from `assets/media/` and per-author `avatar.*` into
  `quarto/images/`. The old hero/section background images and parallax effects
  were not reproduced (clean Quarto defaults instead).
- **Two Quarto extensions installed** (vendored in `quarto/_extensions/`):
  `quarto-ext/fontawesome` and `schochastics/academicons`, used for the people
  social icons. Both are standard, widely used, and require no configuration.

## Old files retained (delete only when you're satisfied)
`content/`, `config/`, `assets/`, `static/`, `layouts`-related files, `go.mod`,
`theme.toml`, `.github/workflows/` (Hugo), and `netlify.toml.hugo.bak`.
