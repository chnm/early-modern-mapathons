# Early Modern Mapathons

Static [Hugo](https://gohugo.io/) site for **Early Modern Mapathons**
([emm.rrchnm.org](https://emm.rrchnm.org/)) — team-based GIS training and
transformation of seventeenth-century maps, a Virginia 4-VA project of George Mason
University and Virginia Tech.

This site was migrated from WordPress. It intentionally ships **no theme** and **no
CSS** — bare, semantic HTML templates that build to a deployable static site.

## Requirements

- **Hugo extended `0.161.1`** (pinned to match the production container build)
- Node.js (only for the `npm ci` step in the Docker build; the site itself has no JS
  dependencies)

## Local development

```sh
hugo server -D      # live-reload dev server at http://localhost:1313/
hugo                 # one-off build into ./public/
```

## Project structure

```
hugo.toml                 # site config: baseURL, menu, params (incl. contact email)
content/
  _index.md                             # home page (goals, organizers, updates, get involved)
  mapathon-events.md                    # /mapathon-events/  (standalone page)
  gis-teaching-learning-resources.md    # /gis-teaching-learning-resources/  (standalone "Resources" page)
  explore-early-modern-mapping-projects.md  # /explore-early-modern-mapping-projects/  (standalone "Mapping Projects" page)
  tutorials/              # /tutorials/  (4 tutorial posts)
  announcements/          # /announcements/  (dated news posts)
layouts/                  # bare, unstyled HTML templates (baseof/single/list/index + partials)
static/images/            # extracted media: logo, hero, team headshots, poster, tutorial screenshots
Dockerfile                # stagex hugo-extended build → Caddy static server
.github/workflows/cicd.yml
```

## Content

- Posts use `publishDate` in front matter; the CI `check` job keys on it for scheduled
  publishing. Set a future `publishDate` to schedule a post.
- Old WordPress date-based URLs are preserved via `aliases:` in each post's front
  matter (e.g. `/2026/04/12/introduction-to-gis-tutorial/` → `/tutorials/introduction-to-gis/`).
- Contact forms were **not** migrated; the site links to `chnm@gmu.edu` instead.

## Deployment

Deployment follows the CHNM pattern (mirrors `chnm/rrchnm.org`): pushes to `main` /
`preview` (and a weekday cron) trigger `.github/workflows/cicd.yml`, which calls the
shared reusable workflows to build a container image (`Dockerfile`) and deploy it.

The `Dockerfile` builds the site with Hugo extended `0.161.1` and serves `public/` with
Caddy. Because the build runs `npm ci`, keep `package.json` and `package-lock.json` in
sync.

Hosts: production `emm.rrchnm.org`, dev/staging `emm.dev.chnm.gmu.edu`.
