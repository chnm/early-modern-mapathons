# Basic Theme (Parchment/Ink) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a basic map/archival-inspired visual theme (white background, warm ink/oxblood palette, self-hosted 17th-century display typeface) to the currently unstyled Early Modern Mapathons Hugo site, plus a small team-card grid on the homepage.

**Architecture:** One CSS file (`assets/css/style.css`) processed through Hugo Pipes and linked from `head.html`. Styling relies on the site's existing semantic HTML (`header`, `footer`, `nav`, `main`, `article`, `hr`) — no new classes are needed on `header.html`/`footer.html`/`list.html`/`single.html`. Two new files change: `baseof.html` gets a `home` body class (for the hero-image treatment), and two new shortcodes (`team`, `team-member`) let `content/_index.md` render the team section as a card grid instead of raw repeated markdown.

**Tech Stack:** Hugo extended 0.161.1 (Hugo Pipes: `resources.Get`, `resources.Fingerprint`), plain CSS (no framework, no preprocessor), self-hosted IM Fell English webfont (OFL license), Hugo shortcodes (Go templates).

## Global Constraints

- Hugo extended `0.161.1` (matches production; do not rely on features from a newer version).
- Background is plain white `#ffffff` (not parchment-tinted) — confirmed by user after spec review.
- Ink (body/heading text) is warm near-black `#241f1a`, not pure black.
- Accent (links, small details) is muted oxblood `#8a3a2b`, hover `#6e2e21`.
- Rule/border color is soft sepia-brown `#c9b79c`, not gray.
- Headings use self-hosted **IM Fell English** only — no Google Fonts CDN link, no other external font/network dependency added to the site.
- Body text uses the system sans-serif stack only.
- No CSS framework, no JS added anywhere.
- No changes to `hugo.toml`, the `Dockerfile`, or `.github/workflows/cicd.yml` — the existing `hugo --minify` build already runs Hugo Pipes correctly.
- No new content pages; only `content/_index.md`'s existing team section is rewritten (to use the new shortcodes), all other content files are untouched.
- All work happens on branch `theme/basic-parchment-ink` (already created).

---

## File Structure

- Create: `static/fonts/IMFellEnglish-Regular.woff2`, `static/fonts/IMFellEnglish-Italic.woff2` — self-hosted font binaries.
- Create: `assets/css/style.css` — the entire stylesheet (variables, reset, base typography, header/footer/nav, hero, team grid, lists/single pages). Built up incrementally across Tasks 2–6.
- Modify: `layouts/partials/head.html` — link the compiled/fingerprinted stylesheet.
- Modify: `layouts/_default/baseof.html` — add `class="home"` to `<body>` when `.IsHome`.
- Create: `layouts/shortcodes/team.html` — grid wrapper shortcode.
- Create: `layouts/shortcodes/team-member.html` — single team-card shortcode.
- Modify: `content/_index.md` — rewrite the "Project Organizers and Affiliates" section to use the two shortcodes above.
- Create (dev-only, not committed to app code): `.claude/launch.json` — Hugo dev server config for the Browser preview tool, used for visual QA in Tasks 3–7.
- Move: `gmu-logo.png`, `rrchnm-logo.png` (dropped at repo root by the user mid-implementation) → `static/images/gmu-logo.png`, `static/images/rrchnm-logo.png` — added in Task 8.
- Modify: `layouts/partials/footer.html` — add the two logos, linked, below the attribution paragraph. Added in Task 8.

## Global Verification Method

Every task is verified two ways:
1. **Build check:** `hugo` (from repo root) exits 0 with no `ERROR` lines in output.
2. **Visual check:** the Hugo dev server (`hugo server -D`, port 1313) is previewed in the Browser tool and the specific change is confirmed on the relevant page (via `read_page`/`get_page_text`/screenshot, and `javascript_tool` for computed-style/network spot checks).

Task 1 sets up the dev-server launch config once; later tasks reuse it.

---

### Task 1: Self-host IM Fell English + dev server config

**Files:**
- Create: `static/fonts/IMFellEnglish-Regular.woff2`
- Create: `static/fonts/IMFellEnglish-Italic.woff2`
- Create: `.claude/launch.json`

**Interfaces:**
- Produces: two font files at `/fonts/IMFellEnglish-Regular.woff2` and `/fonts/IMFellEnglish-Italic.woff2` (site-root-relative URLs, since `static/` is served as `/`) — Task 2's `@font-face` rules reference these exact paths.

- [ ] **Step 1: Download the two woff2 files**

These are Google Fonts' own hosted OFL-licensed binaries for IM Fell English (regular + italic, weight 400) — downloading them directly and committing them is the standard self-hosting approach (no runtime CDN dependency after this).

```bash
mkdir -p static/fonts
curl -sL "https://fonts.gstatic.com/s/imfellenglish/v14/Ktk1ALSLW8zDe0rthJysWrnLsAzHEKOYd4xI5g.woff2" -o static/fonts/IMFellEnglish-Regular.woff2
curl -sL "https://fonts.gstatic.com/s/imfellenglish/v14/Ktk3ALSLW8zDe0rthJysWrnLsAzHFZOafYtq5Mzh.woff2" -o static/fonts/IMFellEnglish-Italic.woff2
```

- [ ] **Step 2: Verify the files downloaded correctly**

Run: `file static/fonts/IMFellEnglish-Regular.woff2 static/fonts/IMFellEnglish-Italic.woff2`
Expected: both report `Web Open Font Format (Version 2)` (not `HTML document` or `ASCII text` — that would mean the download failed and saved an error page instead).

- [ ] **Step 3: Create the Hugo dev-server launch config**

```json
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "hugo-dev",
      "runtimeExecutable": "hugo",
      "runtimeArgs": ["server", "-D"],
      "port": 1313
    }
  ]
}
```

- [ ] **Step 4: Start the preview and confirm the font files are servable**

Use the Browser tool: `preview_start` with `name: "hugo-dev"`, then `preview_logs` to confirm no startup errors, then navigate to `http://localhost:1313/fonts/IMFellEnglish-Regular.woff2` directly and confirm the response isn't a 404 (a binary font download / no error page).

- [ ] **Step 5: Commit**

```bash
git add static/fonts/IMFellEnglish-Regular.woff2 static/fonts/IMFellEnglish-Italic.woff2 .claude/launch.json
git commit -m "Add self-hosted IM Fell English font files and dev server config"
```

---

### Task 2: CSS foundation (variables, reset, base typography) + Hugo Pipes wiring

**Files:**
- Create: `assets/css/style.css`
- Modify: `layouts/partials/head.html`

**Interfaces:**
- Consumes: `/fonts/IMFellEnglish-Regular.woff2`, `/fonts/IMFellEnglish-Italic.woff2` (Task 1).
- Produces: CSS custom properties `--color-bg`, `--color-ink`, `--color-accent`, `--color-accent-hover`, `--color-rule`, `--font-heading`, `--font-body`, `--measure` — every later task's CSS uses these instead of hardcoding colors/fonts.

- [ ] **Step 1: Write `assets/css/style.css`**

```css
@font-face {
  font-family: "IM Fell English";
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url("/fonts/IMFellEnglish-Regular.woff2") format("woff2");
}

@font-face {
  font-family: "IM Fell English";
  font-style: italic;
  font-weight: 400;
  font-display: swap;
  src: url("/fonts/IMFellEnglish-Italic.woff2") format("woff2");
}

:root {
  --color-bg: #ffffff;
  --color-ink: #241f1a;
  --color-muted: #5a4f45;
  --color-accent: #8a3a2b;
  --color-accent-hover: #6e2e21;
  --color-rule: #c9b79c;
  --font-heading: "IM Fell English", Georgia, "Times New Roman", serif;
  --font-body: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
  --measure: 42rem;
}

*, *::before, *::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: var(--color-bg);
  color: var(--color-ink);
  font-family: var(--font-body);
  font-size: 1.125rem;
  line-height: 1.6;
}

main {
  max-width: var(--measure);
  margin: 0 auto;
  padding: 0 1.5rem 4rem;
}

h1, h2, h3 {
  font-family: var(--font-heading);
  font-weight: 400;
  line-height: 1.2;
  color: var(--color-ink);
}

h1 { font-size: 2.5rem; margin: 0 0 1rem; }
h2 { font-size: 1.75rem; margin: 2.5rem 0 1rem; }
h3 { font-size: 1.35rem; margin: 1.5rem 0 0.5rem; }

p { margin: 0 0 1.25rem; }

a {
  color: var(--color-accent);
  text-decoration: underline;
  text-underline-offset: 0.15em;
}

a:hover { color: var(--color-accent-hover); }

img {
  max-width: 100%;
  height: auto;
  display: block;
}

hr {
  border: none;
  border-top: 1px solid var(--color-rule);
  max-width: var(--measure);
  margin: 0 auto;
}
```

- [ ] **Step 2: Wire the stylesheet into `head.html` via Hugo Pipes**

Read the current file first, then add the stylesheet link inside `<head>...</head>`, right before `</head>`:

```html
  {{ with resources.Get "css/style.css" }}
    {{ $css := . | resources.Fingerprint "sha256" }}
    <link rel="stylesheet" href="{{ $css.RelPermalink }}" integrity="{{ $css.Data.Integrity }}">
  {{ end }}
</head>
```

- [ ] **Step 3: Build check**

Run: `hugo` from the repo root.
Expected: exit code 0, no `ERROR` lines. Confirm `public/css/style.<hash>.css` was generated: `ls public/css/`.

- [ ] **Step 4: Visual check**

With the dev server running (Task 1's `preview_start`), navigate to `http://localhost:1313/` and use `read_console_messages` to confirm no 404s for the stylesheet, then use `javascript_tool` to confirm the computed background/font took effect:

```js
getComputedStyle(document.body).backgroundColor
getComputedStyle(document.body).fontFamily
```

Expected: background resolves to `rgb(255, 255, 255)`; font-family includes the system sans stack (not the browser default serif).

- [ ] **Step 5: Commit**

```bash
git add assets/css/style.css layouts/partials/head.html
git commit -m "Add base theme stylesheet and wire it through Hugo Pipes"
```

---

### Task 3: Header, footer, and nav styling

**Files:**
- Modify: `assets/css/style.css` (append)

**Interfaces:**
- Consumes: `--color-ink`, `--color-accent`, `--color-muted`, `--color-rule`, `--font-heading`, `--measure` (Task 2).
- Produces: none consumed by later tasks — this section is self-contained.

- [ ] **Step 1: Append header/footer/nav rules to `assets/css/style.css`**

```css
header {
  max-width: var(--measure);
  margin: 0 auto;
  padding: 2rem 1.5rem 1.5rem;
}

header img { width: 120px; }

header p {
  font-family: var(--font-heading);
  font-size: 1.15rem;
  margin: 0.75rem 0 1.25rem;
}

header nav ul,
footer nav ul {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem 1.5rem;
  margin: 0;
  padding: 0;
}

header nav a,
footer nav a {
  text-decoration: none;
  color: var(--color-ink);
  font-size: 0.95rem;
  letter-spacing: 0.02em;
  text-transform: uppercase;
}

header nav a:hover,
footer nav a:hover {
  color: var(--color-accent);
  text-decoration: underline;
}

footer {
  max-width: var(--measure);
  margin: 0 auto;
  padding: 1.5rem 1.5rem 3rem;
  font-size: 0.9rem;
  color: var(--color-muted);
}

footer nav { margin-bottom: 1rem; }
footer p { margin: 0 0 0.5rem; }
```

- [ ] **Step 2: Build check**

Run: `hugo`. Expected: exit code 0, no `ERROR` lines.

- [ ] **Step 3: Visual check**

Navigate to `http://localhost:1313/` in the Browser tool, take a screenshot, and confirm: logo is ~120px wide, nav links are horizontal and uppercase, the sepia `<hr>` rules under the header and above the footer are visible (not full-width black), footer text is smaller/muted.

- [ ] **Step 4: Commit**

```bash
git add assets/css/style.css
git commit -m "Style header, footer, and nav"
```

---

### Task 4: Homepage hero image treatment

**Files:**
- Modify: `layouts/_default/baseof.html`
- Modify: `assets/css/style.css` (append)

**Interfaces:**
- Consumes: `--color-rule` (Task 2).
- Produces: `body.home` selector hook — no later task depends on this, but note it exists in case a future homepage-only style is needed.

- [ ] **Step 1: Add the `home` body class in `baseof.html`**

Read the file first. Change:

```html
<body>
```

to:

```html
<body{{ if .IsHome }} class="home"{{ end }}>
```

- [ ] **Step 2: Append hero styling to `assets/css/style.css`**

The homepage's first line of content is a standalone Markdown image (`![Early Modern Mapathons](/images/hero.jpg)`), which Goldmark renders as `<main><p><img ...></p>...`. This selector targets exactly that image, only on the homepage:

```css
.home main > p:first-child {
  margin: 0 0 2.5rem;
}

.home main > p:first-child img {
  border: 1px solid var(--color-rule);
  width: 100%;
}
```

- [ ] **Step 3: Build check**

Run: `hugo`. Expected: exit code 0, no `ERROR` lines.

- [ ] **Step 4: Visual check**

Navigate to `http://localhost:1313/` and confirm the hero map image has a thin sepia border and spans the content width. Then navigate to `http://localhost:1313/tutorials/` (a non-home page) and confirm `<body>` has no `class="home"` (via `read_page` or `javascript_tool: document.body.className`) and no page there is affected by the hero rule.

- [ ] **Step 5: Commit**

```bash
git add layouts/_default/baseof.html assets/css/style.css
git commit -m "Add hero image treatment on the homepage"
```

---

### Task 5: Team card shortcodes + homepage content rewrite + grid CSS

**Files:**
- Create: `layouts/shortcodes/team.html`
- Create: `layouts/shortcodes/team-member.html`
- Modify: `content/_index.md`
- Modify: `assets/css/style.css` (append)

**Interfaces:**
- Consumes: `--color-rule`, `--color-muted` (Task 2).
- Produces: shortcode `{{< team >}}...{{< /team >}}` wrapping any number of `{{< team-member name="..." affiliation="..." image="..." links="Label|URL, Label|URL" >}}bio markdown{{< /team-member >}}` calls. Params: `name`, `affiliation`, `image` are plain strings; `links` is a comma-separated list of `Label|URL` pairs (spaces around each pair are trimmed).

- [ ] **Step 1: Write `layouts/shortcodes/team.html`**

```html
<div class="team-grid">
{{ .Inner }}
</div>
```

- [ ] **Step 2: Write `layouts/shortcodes/team-member.html`**

```html
<div class="team-card">
  {{ with .Get "image" }}<img src="{{ . }}" alt="{{ $.Get "name" }}" class="team-card__photo">{{ end }}
  <h3 class="team-card__name">{{ .Get "name" }}</h3>
  {{ with .Get "affiliation" }}<p class="team-card__affiliation">{{ . }}</p>{{ end }}
  <div class="team-card__bio">
    {{ .Inner | markdownify }}
  </div>
  {{ with .Get "links" }}
  <p class="team-card__links">
    {{ $links := strings.Split . "," }}
    {{ range $i, $link := $links }}
      {{ $trimmed := strings.TrimSpace $link }}
      {{ $parts := strings.Split $trimmed "|" }}
      {{ if eq (len $parts) 2 }}{{ if $i }} &middot; {{ end }}<a href="{{ index $parts 1 }}">{{ index $parts 0 }}</a>{{ end }}
    {{ end }}
  </p>
  {{ end }}
</div>
```

- [ ] **Step 3: Rewrite the team section in `content/_index.md`**

Read the file first (team section runs from `## Project Organizers and Affiliates` through the blank line before `## Updates`). Replace that whole section with:

```markdown
## Project Organizers and Affiliates

{{< team >}}

{{< team-member name="Dr. Rachel Midura" affiliation="Virginia Tech" image="/images/team/midura.jpg" links="Website|https://rachelmidura.com, Academia.edu|https://vt.academia.edu/RachelMidura, Bluesky|https://bsky.app/profile/rmidura.bsky.social" >}}
Assistant Professor of Digital and Early Modern European history. Her research areas
include the history of travel, diplomacy, espionage, and information/communication in
early modern Italy and the Habsburg Empire.
{{< /team-member >}}

{{< team-member name="Dr. Amanda Madden" affiliation="George Mason University" image="/images/team/madden.jpg" links="Bluesky|https://bsky.app/profile/amandamadden.bsky.social" >}}
Assistant Professor of History and Affiliate Faculty at the Roy Rosenzweig Center for
History and New Media (RRCHNM). Her research areas include digital spatial history,
digital humanities, early modern Italy, the history of crime and violence, the history
of women and gender, and the scholarship of teaching and learning.
{{< /team-member >}}

{{< team-member name="Dr. Jessica Taylor" affiliation="Virginia Tech" image="/images/team/taylor.jpg" links="Website|https://www.jessicaltaylor.com/" >}}
Associate Professor of Oral and Public History. Her research interests include early
American history, historic preservation, and native American history.
{{< /team-member >}}

{{< team-member name="Dr. Jessica Otis" affiliation="George Mason University" image="/images/team/otis.jpg" links="Website|https://jessicaotis.com/, Bluesky|https://bsky.app/profile/jmotis.bsky.social" >}}
Assistant Professor of History and the Director of Public Projects at the Roy
Rosenzweig Center for History and New Media. Her research interests include
mathematics, plague, and cryptography in early modern England.
{{< /team-member >}}

{{< /team >}}
```

- [ ] **Step 4: Append team grid CSS to `assets/css/style.css`**

```css
.team-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 2rem;
  margin: 1.5rem 0 2.5rem;
}

.team-card__photo {
  width: 100%;
  aspect-ratio: 1 / 1;
  object-fit: cover;
  border: 1px solid var(--color-rule);
  margin-bottom: 0.75rem;
}

.team-card__name { margin: 0 0 0.15rem; font-size: 1.25rem; }

.team-card__affiliation {
  margin: 0 0 0.5rem;
  font-style: italic;
  color: var(--color-muted);
  font-size: 0.95rem;
}

.team-card__bio p { font-size: 1rem; margin: 0 0 0.75rem; }

.team-card__links { font-size: 0.9rem; margin: 0; }
```

- [ ] **Step 5: Build check**

Run: `hugo`. Expected: exit code 0, no `ERROR` lines (a shortcode typo here would fail the build loudly — confirm it doesn't).

- [ ] **Step 6: Visual check**

Navigate to `http://localhost:1313/` and confirm: 4 team cards render in a responsive grid (photo, name, italic affiliation, bio, link list separated by `·`), all four photos and all links (e.g. Rachel Midura's 3 links, Amanda Madden's 1 link) are present and correctly labeled. Resize the Browser viewport to `mobile` (375px) and confirm the grid collapses to a single column without overflow.

- [ ] **Step 7: Commit**

```bash
git add layouts/shortcodes/team.html layouts/shortcodes/team-member.html content/_index.md assets/css/style.css
git commit -m "Add team card shortcodes and rewrite homepage team section"
```

---

### Task 6: List and single page styling

**Files:**
- Modify: `assets/css/style.css` (append)

**Interfaces:**
- Consumes: `--color-rule`, `--color-muted`, `--font-heading` (Task 2).
- Produces: none — final CSS section.

- [ ] **Step 1: Append list/single page rules to `assets/css/style.css`**

```css
main ul {
  list-style: none;
  margin: 1.5rem 0;
  padding: 0;
}

main ul li {
  padding: 1rem 0;
  border-top: 1px solid var(--color-rule);
}

main ul li:first-child { border-top: none; }

main ul li a {
  font-family: var(--font-heading);
  font-size: 1.2rem;
  text-decoration: none;
}

main ul li a:hover { text-decoration: underline; }

main ul li time {
  display: block;
  font-size: 0.85rem;
  color: var(--color-muted);
  margin-top: 0.25rem;
}

article time {
  display: block;
  color: var(--color-muted);
  font-size: 0.95rem;
  margin-bottom: 1.5rem;
}
```

- [ ] **Step 2: Build check**

Run: `hugo`. Expected: exit code 0, no `ERROR` lines.

- [ ] **Step 3: Visual check**

Navigate to `http://localhost:1313/tutorials/` and confirm: each tutorial is a stacked list item (heading-styled link, date below it, sepia divider between items, no divider above the first item). Navigate to a single tutorial, e.g. `http://localhost:1313/tutorials/introduction-to-gis/`, and confirm the date renders as muted small text above the article body. Repeat for `http://localhost:1313/announcements/`.

- [ ] **Step 4: Commit**

```bash
git add assets/css/style.css
git commit -m "Style list and single page templates"
```

---

### Task 7: Full-site QA pass

**Files:** none (verification only; fixes only if QA finds a real defect, in whichever file is at fault).

**Interfaces:** none.

- [ ] **Step 1: Build check**

Run: `hugo --minify` (the exact command used in `package.json`'s `build` script and the Dockerfile). Expected: exit code 0, no `ERROR` lines. Then run `ls public/css/` and confirm exactly one fingerprinted `style.*.css` file exists (no stale duplicates from earlier dev builds).

- [ ] **Step 2: Visual pass over every page**

With the dev server running, navigate to and screenshot each of: `/`, `/mapathon-events/`, `/tutorials/`, `/tutorials/introduction-to-gis/`, `/gis-teaching-learning-resources/`, `/explore-early-modern-mapping-projects/`, `/announcements/`, `/announcements/autumn-info-session-virginia-tech/`. Confirm on each: white background, ink-colored body text, oxblood links, IM Fell English headings (visually distinct serif, not the body sans-serif), consistent header/nav/footer.

- [ ] **Step 3: Font-loading spot check**

On any page, use `read_network_requests` filtered to `urlPattern: "fonts"` and confirm both `IMFellEnglish-Regular.woff2` and `IMFellEnglish-Italic.woff2` (the italic is used by `.team-card__affiliation`) return `200`.

- [ ] **Step 4: Responsive spot check**

`resize_window` to `mobile` (375×812) and re-check `/` and `/tutorials/`: nav wraps sensibly, hero image and team grid stay within viewport width (no horizontal scroll), text stays readable.

- [ ] **Step 5: Contrast spot check**

Confirm body text contrast is comfortably AA: ink `#241f1a` on white `#ffffff` — well above the 4.5:1 minimum by construction (near-black on white), so this is a visual sanity check rather than a calculation. Also confirm oxblood link color `#8a3a2b` on white reads clearly.

- [ ] **Step 6: Stop the preview server**

Use `preview_stop` on the `hugo-dev` server started in Task 1.

- [ ] **Step 7: Final commit (only if Step 2–5 found and required a fix)**

```bash
git add -A
git commit -m "Fix QA findings from full-site theme pass"
```

If no defects were found, skip this step — there's nothing to commit.

---

### Task 8: Footer partner logos (GMU, RRCHNM)

Added mid-implementation: the user dropped `gmu-logo.png` and `rrchnm-logo.png` at the repo root and asked for them in the footer, linked to the respective institution sites, placed below the existing attribution paragraph.

**Files:**
- Move: `gmu-logo.png` → `static/images/gmu-logo.png`
- Move: `rrchnm-logo.png` → `static/images/rrchnm-logo.png`
- Modify: `layouts/partials/footer.html`
- Modify: `assets/css/style.css` (append)

**Interfaces:**
- Consumes: none from earlier tasks beyond the existing `footer` CSS from Task 3 (this task adds a new `.footer-logos` class, since this is new content, not just styling of existing markup).
- Produces: none consumed later.

- [ ] **Step 1: Move the logo files into `static/images/`**

```bash
mkdir -p static/images
git mv gmu-logo.png static/images/gmu-logo.png
git mv rrchnm-logo.png static/images/rrchnm-logo.png
```

(If `git mv` fails because the files aren't yet tracked, use `mv` instead and `git add` both destination paths in the commit step.)

- [ ] **Step 2: Add the logos to `layouts/partials/footer.html`**

Read the file first. The current file is:

```html
<hr>
<footer>
  <nav aria-label="Footer navigation">
    <ul>
      {{ range .Site.Menus.main }}<li><a href="{{ .URL }}">{{ .Name }}</a></li>{{ end }}
    </ul>
  </nav>
  <p>Contact: <a href="mailto:{{ .Site.Params.email }}">{{ .Site.Params.email }}</a></p>
  <p>
    Early Modern Mapathons is a project of George Mason University and Virginia Tech,
    supported by a Virginia 4-VA grant, in affiliation with the
    <a href="https://rrchnm.org/">Roy Rosenzweig Center for History and New Media</a>.
    Results are shared under a
    <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/">CC BY-NC-SA 4.0</a> license.
  </p>
</footer>
```

Add a new `<p class="footer-logos">` immediately after the existing attribution `<p>...</p>`, before `</footer>`:

```html
  <p class="footer-logos">
    <a href="https://www.gmu.edu"><img src="/images/gmu-logo.png" alt="George Mason University"></a>
    <a href="https://rrchnm.org"><img src="/images/rrchnm-logo.png" alt="Roy Rosenzweig Center for History and New Media"></a>
  </p>
```

- [ ] **Step 3: Append footer-logo CSS to `assets/css/style.css`**

```css
.footer-logos {
  display: flex;
  align-items: center;
  gap: 2rem;
  margin-top: 1.5rem;
}

.footer-logos img {
  height: 40px;
  width: auto;
}
```

- [ ] **Step 4: Build check**

Run: `hugo`. Expected: exit code 0, no `ERROR` lines. Confirm the images copied into `public/images/`: `ls public/images/gmu-logo.png public/images/rrchnm-logo.png`.

- [ ] **Step 5: Visual check**

With the dev server running, navigate to `http://localhost:1313/` (or any page — the footer is site-wide) and confirm: both logos render side by side below the attribution paragraph, each at 40px height with proportional width, and each is a working link (`href` present, pointing to `https://www.gmu.edu` and `https://rrchnm.org` respectively — confirm via `read_page` rather than clicking through).

- [ ] **Step 6: Commit**

```bash
git add static/images/gmu-logo.png static/images/rrchnm-logo.png layouts/partials/footer.html assets/css/style.css
git commit -m "Add GMU and RRCHNM logos to the footer"
```

---

### Task 9: Footer partner logos (Virginia Tech, 4-VA)

Added mid-implementation, right after Task 8: the user asked for two more institutional logos in the footer — Virginia Tech and 4-VA (the grant program funding the project, per `content/_index.md`'s "supported by a Virginia 4-VA grant") — sourced from URLs the user provided. With 4 logos now in one row, `.footer-logos` also needs `flex-wrap` so it doesn't overflow on narrow viewports.

**Files:**
- Create: `static/images/4-va-logo.svg`
- Create: `static/images/vt-logo.png`
- Modify: `layouts/partials/footer.html`
- Modify: `assets/css/style.css` (the existing `.footer-logos` rule from Task 8 gains one property — this is the one CSS edit in the whole plan that isn't a pure append)

**Interfaces:**
- Consumes: the `.footer-logos` / `.footer-logos img` rules from Task 8.
- Produces: none consumed later.

- [ ] **Step 1: Download and prepare the two logo files**

The Virginia Tech source URL serves a WebP image despite its `.png`-looking filename — convert it to a true PNG so it matches the other footer logos' format:

```bash
curl -sL "https://p.kagi.com/proxy/4-va-logo-final.svg?c=Em_yfUS9HEEB6vElAuHaNURLXfgVbnO07wp8kjj8Y4U3IZydMApVCbUVFPXPEKwWOtI2UHEYaqJJ3fmSwC9Los9-YBSTxvnSlDuNY1S5qyVfsd24DMfmwzcuPp8zzAnZ5gN6wNJqpJmHTnYRqw1kXA%3D%3D" -o static/images/4-va-logo.svg
curl -sL "https://p.kagi.com/proxy/3840px-Virginia_Tech_logo.svg.png?c=9cn5Kxse4yD05EJkf6QML9dK4clUbdQ9Oq4d5gDoyHBwiX43u0CCAEVi8DMCHFAXjJZTma46O1sVILX0dlbOJn_bbWWtEVTqTkELa8Ar1VVF23yMF_dAA6lJHZo5LGYyn8vIG8fNTEgscwxfSpA3UQQkZ9aZ7QORCRzmAzDmnzc%3D" -o /tmp/vt-logo-raw
sips -s format png /tmp/vt-logo-raw --out static/images/vt-logo.png
rm /tmp/vt-logo-raw
```

- [ ] **Step 2: Verify the downloads**

Run: `file static/images/4-va-logo.svg static/images/vt-logo.png`
Expected: the first reports `SVG Scalable Vector Graphics image`, the second reports `PNG image data` (not `Web/P image` — that would mean the `sips` conversion step was skipped).

- [ ] **Step 3: Add the two logos to `layouts/partials/footer.html`**

Read the file first — Task 8 left it with a `<p class="footer-logos">` block containing the GMU and RRCHNM logos. Replace that block (matching the site's existing prose order — "George Mason University and Virginia Tech ... 4-VA grant ... RRCHNM") with:

```html
  <p class="footer-logos">
    <a href="https://www.gmu.edu"><img src="/images/gmu-logo.png" alt="George Mason University"></a>
    <a href="https://www.vt.edu"><img src="/images/vt-logo.png" alt="Virginia Tech"></a>
    <a href="https://4-va.org"><img src="/images/4-va-logo.svg" alt="4-VA"></a>
    <a href="https://rrchnm.org"><img src="/images/rrchnm-logo.png" alt="Roy Rosenzweig Center for History and New Media"></a>
  </p>
```

- [ ] **Step 4: Add `flex-wrap` to the existing `.footer-logos` rule in `assets/css/style.css`**

Find the `.footer-logos` rule added in Task 8:

```css
.footer-logos {
  display: flex;
  align-items: center;
  gap: 2rem;
  margin-top: 1.5rem;
}
```

Add one property so 4 logos wrap on narrow viewports instead of overflowing:

```css
.footer-logos {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 2rem;
  margin-top: 1.5rem;
}
```

Do not touch the `.footer-logos img` rule below it — it already applies to all four images.

- [ ] **Step 5: Build check**

Run: `hugo`. Expected: exit code 0, no `ERROR` lines. Confirm `public/images/4-va-logo.svg` and `public/images/vt-logo.png` exist.

- [ ] **Step 6: Visual check**

With the dev server running, navigate to `http://localhost:1313/` and confirm via `read_page`: all 4 logos render as links with correct hrefs (gmu.edu, vt.edu, 4-va.org, rrchnm.org) in that order, each ~40px tall. Then `resize_window` to `mobile` (375×812) and confirm the row wraps to two lines instead of overflowing horizontally (`document.body.scrollWidth` should equal `375`, not exceed it).

- [ ] **Step 7: Commit**

```bash
git add static/images/4-va-logo.svg static/images/vt-logo.png layouts/partials/footer.html assets/css/style.css
git commit -m "Add Virginia Tech and 4-VA logos to the footer"
```
