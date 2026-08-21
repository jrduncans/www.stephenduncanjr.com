# Phase 2: Legacy content separation

Status: ready

## Objective

Separate current-era publishing from the previous site without moving or
rewriting historical post files. Preserve legacy URLs while making old material
available only through a quiet archive and direct links.

## Content rules

- A post with `era: current` is current.
- A post without `era: current` is legacy.
- Every post present before this relaunch is legacy.
- New posts must use an explicit `/posts/<slug>/` permalink rather than inherit
  the legacy dated `.html` pattern.
- Legacy non-post pages must be classified by front matter, path-scoped defaults,
  layout, or a small data file. Choose the least invasive maintainable option.
- Do not add `era: legacy` to all 276 existing posts.

## Scope

1. Update homepage selection so only current-era posts appear.
2. Replace the plugin-generated all-post feed if necessary so `/feed.xml`
   contains only current-era posts.
3. Ensure any sitemap introduced by the relaunch contains only current public
   pages and current posts.
4. Create `/archive/` with a neutral heading such as "Previous site, 2002–2018."
5. Group legacy posts by year and title. Add restrained sections for the former
   writing, pictures, projects, Firefox, PDF, and other preserved pages.
6. Add `noindex,follow` to:
   - every legacy individual post;
   - every preserved legacy non-post page;
   - old month and tag archive pages;
   - `/archive/` itself;
   - compatibility redirect pages and the 404 page.
7. Preserve required paths from Phase 1, including dated post URLs.
8. Provide a static compatibility response for `/feeds/posts/default`.
9. Remove legacy tag/month links from primary navigation, but keep their pages
   if required by the compatibility manifest.

The footer link to `/archive/` is implemented visually in Phase 3. The archive
must still be reachable directly during this phase.

## Search-engine behavior

Do not block legacy pages in `robots.txt`; crawlers must be able to read their
`noindex` directive. Do not add legacy URLs to a sitemap. Canonical URLs for
legacy pages must continue to point to their own preserved HTTPS `www` URL, not
to the archive page.

## Empty current era

It is acceptable for the current homepage and feed to have no posts during
implementation. Do not create or publish a fake relaunch post to fill the gap.
Production cutover is blocked until a substantive post exists.

## Verification

Add automated checks proving:

- all Phase 1 compatibility paths remain present;
- a known legacy post is absent from the homepage and feed;
- a known legacy post contains `noindex` and a self-canonical URL;
- the archive contains representative posts and auxiliary pages;
- the archive itself is `noindex`;
- a temporary current-era fixture, test page, or isolated test build appears on
  the homepage and in the feed but not in the legacy section;
- the fixture does not survive in production source or output.

## Acceptance criteria

- Existing post bodies and paths are unchanged.
- The normal discovery surface exposes no legacy posts.
- All legacy material is reachable from `/archive/` or documented as an
  intentional exception.
- Legacy pages are public but marked for removal from search indexes.
- The feed is valid XML and contains current-era posts only.
- The full site and compatibility check pass.

## Out of scope

- Restyling pages beyond the minimum markup required for separation.
- Repairing broken historical links or embeds.
- Creating a substantive current post.
- Configuring GitHub Pages or DNS.

## Handoff record

When complete, record the classification mechanism, feed implementation,
archive coverage, intentional exceptions, and verification results here. Mark
Phase 2 complete and Phases 3 and 4 ready in `README.md`.
