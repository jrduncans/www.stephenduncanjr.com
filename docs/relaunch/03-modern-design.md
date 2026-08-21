# Phase 3: Modern design

Status: blocked on Phase 2

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

When complete, record the layout and stylesheet structure, removed runtime
dependencies, browser/viewport checks, remaining isolated legacy media issues,
and verification results here. Mark Phase 3 complete in `README.md`.
