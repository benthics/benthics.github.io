# BEM Website — Hugo

This is the Benthic Ecology Meeting Society website, converted from a static HTML site to [Hugo](https://gohugo.io/).

## Project Structure

```
hugo-bem/
├── hugo.toml               # Site config: baseURL, menus, PayPal/GA params
├── archetypes/
│   └── default.md          # Template for new content files
├── content/
│   ├── _index.md           # Homepage content + hero slider front matter
│   ├── about.md
│   ├── board.md            # Board members + DEI committee (use #dei anchor)
│   ├── pastmeetings.md
│   ├── presenters.md       # Plenary speakers
│   ├── speakers.md         # Presenter instructions
│   ├── bem2024.md          # Archive: 52nd BEM
│   ├── bem2025.md          # Archive: 53rd BEM
│   └── meeting-2026/
│       ├── _index.md       # 54th BEM overview + schedule
│       ├── registration.md
│       ├── fieldtrips.md   # Optional programs
│       ├── abstracts.md
│       ├── travel.md       # Venue, hotels, directions + Google Maps embed
│       ├── program.md      # Oxford Abstracts links + presenter resources
│       ├── agenda.md       # Whova agenda widget embed
│       └── faq.md
├── layouts/
│   ├── index.html          # Homepage layout (FlexSlider hero)
│   ├── _default/
│   │   ├── baseof.html     # Master base template
│   │   ├── single.html     # Standard inner page
│   │   └── list.html       # Section index (e.g. /meeting-2026/)
│   └── partials/
│       ├── head.html       # <head>: meta, CSS, GA, PayPal SDK
│       ├── header.html     # Site nav (Hugo menu system)
│       ├── footer.html     # Site footer
│       ├── page-banner.html # Teal gradient banner for inner pages
│       └── scripts.html    # JS includes at bottom of <body>
└── static/
    ├── css/                # bootstrap, style, custom, font-awesome, etc.
    ├── js/                 # jquery, init, ui-plugins, flexslider, etc.
    ├── colors/             # color1.css … color12.css
    ├── vendor/             # magnific, owl-carousel, flexslider, fullcalendar
    ├── images/             # Site images (large images not included — see below)
    ├── BEMLogos/           # Past meeting logos
    └── *.pdf / *.xlsx      # Downloadable documents
```

## Getting Started

### Prerequisites

Install Hugo (extended version recommended):
```bash
# macOS
brew install hugo

# or download from https://gohugo.io/installation/
```

### Local Development

```bash
cd hugo-bem
hugo server -D
```

Then open http://localhost:1313

### Build for Production

```bash
hugo --minify
```

Output goes to `public/`. Deploy its contents to your web server or GitLab Pages.

## Key Conversion Notes

### JavaScript includes → Hugo partials
The original site used a W3Schools `w3-include-html` JavaScript trick to inject `header.html` into every page at runtime:
```html
<div w3-include-html="header.html"></div>
<script>includeHTML();</script>
```
In Hugo this is replaced by a proper server-side partial:
```
{{- partial "header.html" . -}}
```
The JS include code is gone — no more AJAX header loading.

### Navigation
The nav is now driven by Hugo's menu system in `hugo.toml`. To add, remove, or reorder menu items, edit the `[[menu.main]]` entries there.

### Hero slider (homepage)
The FlexSlider on the homepage is configured via front matter in `content/_index.md`:
```yaml
slides:
  - image: "images/my-photo.jpg"
    heading: "My Heading"
    subheading: "Subtitle text"
    button_url: "/registration/"
    button_text: "Register Now!"
```

### Embedded third-party widgets
These are preserved as raw HTML in Markdown content:
- **Whova agenda** — `content/meeting-2026/agenda.md`
- **Google Maps iframe** — `content/meeting-2026/travel.md`
- **Oxford Abstracts** links — `content/meeting-2026/program.md`
- **PayPal donate button** — `layouts/partials/header.html`

### Images
Large image directories were not included in the conversion. All `<img src="...">` paths are preserved as-is. Place your images in `static/images/` and they will be served at `/images/`.

### Site parameters
Edit `hugo.toml` `[params]` to update:
- `contactEmail` — shown in footer
- `googleAnalyticsID` — GA4 measurement ID
- `paypalClientID` / `paypalButtonID` — PayPal hosted button

## Adding a New Year's Meeting

1. Create `content/meeting-YYYY/_index.md` with the meeting overview
2. Add sub-pages: `registration.md`, `travel.md`, `fieldtrips.md`, `abstracts.md`, `faq.md`, `program.md`
3. Update the menu in `hugo.toml` to point to the new year
4. Archive the current year's content by renaming the folder (e.g. `bem2026.md`)

## GitLab CI/CD

The original `.gitlab-ci.yml` is preserved in the project root. Update it to run `hugo` instead of serving static files directly. A minimal Hugo pipeline:

```yaml
image: registry.gitlab.com/pages/hugo:latest

pages:
  script:
    - hugo --minify
  artifacts:
    paths:
      - public
  only:
    - main
```
# benthics.github.io
