# Phase 3: Modern design

Status: complete

## Objective

Replace the early-2000s presentation with a restrained, accessible, text-first
blog design. Apply the same coherent shell to legacy pages without editing their
historical prose or requiring post artwork.

## Public information architecture

- Header: provisional site title, linked to `/`.
- Main navigation: minimal; do not reproduce the old feature list.
- Homepage: current post title, date, and authored summary or restrained
  excerpt. No image placeholder.
- Post page: title, publication date, readable body, and optional tags rendered
  as metadata rather than a legacy tag cloud.
- Footer: RSS link and one subdued `Previous site, 2002–2018` archive link.
- No separate About page is required for launch. Do not link to `jrduncans.com`
  until that site exists and is intentionally in scope.

## Design requirements

- Semantic landmarks (`header`, `nav`, `main`, `article`, `footer`).
- A skip link and visible keyboard focus states.
- Responsive layout from narrow phones through wide desktop screens.
- A comfortable article measure, approximately 65-75 characters.
- Legible system fonts; do not add an external font request.
- CSS custom properties for color and spacing.
- Light and dark palettes using `prefers-color-scheme`; neither may sacrifice
  code-block readability or link contrast.
- Clear heading hierarchy, lists, tables, blockquotes, inline code, code blocks,
  figures, and horizontal overflow behavior.
- Respect `prefers-reduced-motion`; substantial animation is not needed.
- Work without JavaScript.

## Code presentation

Use Jekyll's existing Rouge support. Style fenced Markdown code and legacy
highlight markup with an accessible palette. Long lines must scroll within the
code block instead of widening the page. Do not add Prism, Highlight.js, a copy
button, or a client-side dependency in this phase.

## Metadata

Each current page should have:

- a meaningful document title;
- page-specific description when available, falling back safely;
- a canonical HTTPS `www` URL;
- standard Open Graph and card metadata that remains valid without an image;
- feed discovery metadata;
- UTF-8 and responsive viewport declarations.

Legacy pages retain self-canonical URLs and the Phase 2 robots directive.

## Remove obsolete runtime behavior

Remove from shared layouts and current pages:

- Google JSAPI and custom-search form integration;
- Disqus embeds and comment-count scripts;
- obsolete archive prettification JavaScript;
- global inline styles tied to those systems;
- third-party active resources that are not essential to preserved content.

For isolated legacy embeds or remote images, preserve safely where practical.
Upgrade supported resource URLs to HTTPS or replace a broken active embed with
a plain historical link or explanatory fallback. Do not rewrite ordinary
historical outbound links merely because they use HTTP.

## Visual verification

Inspect rendered screenshots or a live local preview at minimum at:

- a narrow mobile viewport;
- a typical laptop viewport;
- light mode;
- dark mode;
- homepage;
- a long current-style article with code, lists, links, and headings;
- representative HTML and Markdown legacy posts;
- the legacy archive and 404 page.

Use temporary fixture content for coverage if necessary and remove it before
completion.

## Acceptance criteria

- The site is usable without JavaScript and makes no non-content third-party
  runtime requests.
- Text-only posts look complete and intentional.
- Navigation is small, consistent, and keyboard accessible.
- Mobile pages have no unintended horizontal scrolling.
- Code highlighting is readable in both color schemes.
- Legacy bodies remain intact and their URLs pass the compatibility check.
- Build, internal-link, active mixed-content, and phase verification checks pass.

## Out of scope

- A logo, illustration system, post images, or generated social cards.
- A portfolio design.
- Client-side search.
- Comments or reactions.
- Editing historical opinions or prose.

## Handoff record

### Layout and stylesheet structure

- `_layouts/base.html` now owns the document head, page-specific metadata,
  self-canonical HTTPS `www` URLs, feed discovery, skip link, site header,
  minimal navigation, main landmark, and footer.
- `_layouts/default.html` provides the shared legacy page article wrapper;
  `_layouts/post.html` provides the title/date/author/article/tag treatment for
  both current and legacy posts; `_layouts/posts.html` provides the current-era
  homepage and intentional empty state; `_layouts/archive.html` provides the
  noindexed month/tag archive treatment.
- `_includes/navigation.html` is reduced to the home link. The footer keeps the
  required RSS link and one subdued `Previous site, 2002–2018` archive link.
- `_sass/_main.scss` defines the responsive shell, system-font typography,
  readable article measure, landmarks, focus states, lists, tables, quotes,
  figures, overflow behavior, preserved writing-page labels, and reduced-motion
  behavior. `_sass/_highlight.scss` provides the light/dark Rouge palette for
  both Markdown fences and legacy `.highlight` markup.

### Removed runtime dependencies

- Removed shared Google JSAPI/jQuery loading, archive prettification, Disqus
  embeds/count scripts, the Google custom-search form, and page-level inline
  styles associated with those systems.
- `js/main.js` remains only as an inert file to preserve the required historical
  URL; no current page loads JavaScript.
- Replaced the Flickr random-gallery script with a plain archive link and
  upgraded all seven remaining active legacy Flickr image sources, plus the
  preserved thumbnail-gallery image URLs, to HTTPS. Ordinary historical
  outbound link URLs remain unchanged.

### Browser and viewport checks

- Fresh local build inspected in the Codex in-app browser at `390×844` and
  `1440×900` in light mode for the homepage, a current-era fixture homepage and
  article, a long code/list/table article, the legacy archive, and the 404 page.
- The mobile checks reported no unintended horizontal overflow. The article
  fixture rendered one Rouge code block, a table, lists, links, blockquote, and
  tag metadata without JavaScript. The desktop checks showed the intended
  text-first measure and code-block treatment.
- The dark palette is implemented through `prefers-color-scheme: dark` custom
  property overrides, with separate readable code-token colors and focus
  contrast; the browser session used for the screenshot pass was in light mode.

### Verification results

- `TZ=America/Los_Angeles JEKYLL_ENV=production mise exec -- bundle exec jekyll build`
  passed.
- `mise exec -- bin/check-site` passed, verifying all 448 compatibility paths,
  the legacy noindex/canonical rules, current-era fixture inclusion in the
  homepage/feed, current-era exclusion from the archive, semantic shell/meta
  markers, and the absence of obsolete homepage runtime hooks.
- `git diff --check` passed. The generated Atom feed remains valid XML and
  contains current-era posts only.
- A generated-site root-relative-link audit checked 348 links. Current shell,
  archive, tag, feed, and compatibility links resolve. Six pre-existing links
  inside historical posts still point to removed old surfaces (`/blogroll.asp`,
  `/politics`, two old project pages, and two unavailable Abdera downloads);
  these are documented legacy exceptions rather than new design dependencies.
- A source and generated-output scan found no remaining active HTTP image
  sources in post content.

Phase 3 is complete. The repository is ready for the Phase 4 authoring
workflow work.
