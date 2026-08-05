# Basic theme for Early Modern Mapathons — design

## Context

The site currently ships **no theme and no CSS** (bare semantic HTML, per the
README) — a deliberate choice during the WordPress→Hugo migration. It's a
small academic project site (6 content sections: home, mapathon-events,
tutorials, announcements, resources, mapping-projects) sharing one
header/footer/nav across all pages. This design adds a basic visual theme on
top of the existing structure.

## Direction

Map/archival inspired: parchment and ink, drawn from the site's own assets —
the `hero.jpg` (a 1651 map of "Ould Virginia") and the black ink-line
`emm-logo.svg`. Not costume-y "old-timey" styling — a clean, text-first,
academic layout with warm paper tones and one historically-appropriate
display typeface.

## Palette

- Background: warm parchment, e.g. `#f4ecdc`
- Body text ("ink"): warm near-black, e.g. `#241f1a` (not pure black)
- Accent (links, small details): muted oxblood/rust, e.g. `#8a3a2b`
- Rules/borders: soft sepia-brown, not gray

Exact hex values may be refined slightly during implementation for contrast
(WCAG AA against parchment background), but should stay within this warm
parchment/ink/oxblood family.

## Typography

- **Headings**: IM Fell English (OFL-licensed digitization of the actual
  1670s Fell types) — self-hosted `.woff2` in `static/fonts/`, referenced via
  `@font-face`. Headings only; the typeface's old-style quirks make it
  unsuitable for body copy.
- **Body**: system sans-serif stack (`-apple-system, "Segoe UI", Helvetica,
  Arial, sans-serif`) for long-form readability (tutorials, bios) and zero
  added weight.
- No other web fonts. No Google Fonts CDN — self-hosted only, keeping the
  site's zero-external-network-dependency posture.

## Layout & components

- **Header**: logo (sized down from current raw `width="240"`), tagline, and
  nav retained structurally but styled — nav becomes an inline horizontal
  list, understated link treatment (underline on hover, no pill/box styling).
  Thin sepia rule replaces the bare `<hr>`.
- **Homepage hero**: `hero.jpg` gets a constrained max-width treatment with a
  subtle border, placed directly under the header.
- **Team section** (homepage "Project Organizers and Affiliates"): restructured
  from raw markdown (repeated heading/image/paragraph blocks) into a
  responsive card grid via two new shortcodes:
  - `{{< team >}}...{{< /team >}}` — grid wrapper (`<div class="team-grid">`)
  - `{{< team-member name="..." affiliation="..." image="..." links="Label|URL, Label|URL">}}bio markdown{{</team-member>}}`
    — one card (photo, name, affiliation, bio, links)

  `content/_index.md`'s team section is rewritten to use these shortcodes.
  This is the one place content authoring changes; everything else in
  `content/` is untouched.
- **List pages** (tutorials, announcements): simple stacked list, date +
  title, sepia divider between items — no card boxes.
- **Single pages**: styled title/date/author, readable measure (max-width)
  on body text.
- **Footer**: nav repeated + contact + CHNM attribution, smaller/muted text,
  separated by the sepia rule.
- **Links**: oxblood accent, underlined, darkens on hover — no button-style
  link treatments anywhere.

No new pages, no JS, no structural changes to `baseof.html` beyond what's
needed to load the stylesheet.

## Technical approach

- **CSS**: single stylesheet at `assets/css/style.css`, processed through
  Hugo Pipes (`resources.Get` → `css.Minify` (via `--minify` build flag,
  already used) → `resources.Fingerprint` for cache-busting), linked from
  `layouts/partials/head.html` via `{{ with resources.Get "css/style.css" }}...{{ end }}`.
  No CSS framework, no build tooling beyond Hugo itself.
- **Fonts**: IM Fell English `.woff2` file(s) downloaded from Google Fonts'
  open-source (OFL) repository and committed to `static/fonts/`. No runtime
  CDN dependency.
- **Shortcodes**: `layouts/shortcodes/team.html` and
  `layouts/shortcodes/team-member.html`, per the Team section above.
- **Markup touches**: minor class hooks added to `header.html`, `footer.html`,
  `list.html`, `single.html`, `index.html` for styling — no structural
  rewrites.

## Out of scope

- No changes to `hugo.toml` config (menu, params) beyond what styling needs.
- No new content sections or pages.
- No JS/interactivity.
- No changes to the Dockerfile/CI build process (Hugo Pipes works within the
  existing `hugo --minify` build already used in `package.json`/Dockerfile).
