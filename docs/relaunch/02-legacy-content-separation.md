# Phase 2: Legacy content separation

Status: complete

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

The subdued link to `/archive/` is implemented at the bottom of the current
posts page. The archive must still be reachable directly during this phase.

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

### Classification mechanism

- Posts remain unchanged. The homepage, feed, and archive use `era: current` as
  the only current-post marker; every other post is legacy.
- Legacy non-post pages receive `legacy: true` through the `type: pages` path
  default in `_config.yml`. The homepage opts out with `legacy: false`; the
  archive and error documents opt out of the default but explicitly set
  `noindex: true`.
- Month and tag archive pages are identified by their generated `page.type`
  values and receive `noindex,follow` in the shared base layout. The same
  layout adds a self-canonical HTTPS `www` URL to legacy HTML pages.
- New current posts must carry both `era: current` and an explicit
  `/posts/<slug>/` permalink in their front matter. Existing post front matter
  and bodies were not edited.

### Feed and archive

- `feed.xml` is now a checked-in Atom template rather than the plugin's default
  post selection. It emits at most 15 current-era posts and is valid with zero
  entries while the current era is empty. The `jekyll-feed` plugin remains
  installed for feed discovery metadata and future compatibility.
- `/archive/` groups all legacy posts by publication year and title, then links
  to writing, pictures, projects, Firefox, research PDF, About, and preserved
  compatibility pages. It is `noindex,follow` and is linked only from the
  bottom of the current posts page.
- The existing month and tag archive routes remain present and are noindexed.
  The archive sidebar now derives only from current posts, so legacy month
  links do not appear in normal navigation.

### Compatibility and intentional exceptions

- `/feeds/posts/default/` is a static noindex HTML redirect to `/feed.xml`,
  preserving the former feed route without adding a server runtime. The
  existing S3 redirect remains documented for the pre-cutover deployment.
- `/404.html` is a noindex current error document; the existing `/error.html`
  path remains available and noindexed for baseline compatibility.
- No sitemap or `robots.txt` was introduced in this phase. Legacy URLs remain
  crawlable so crawlers can see their noindex directives.
- Binary/static assets are intentional technical/static exceptions rather than
  archive navigation pages. Historical post and auxiliary HTML pages,
  including the Google ownership token file, are reachable from `/archive/` or
  from the preserved index pages it links.

### Verification results

- `mise exec -- bin/check-site` passes after a fresh production build and
  verifies all 448 Phase 1 compatibility paths.
- The checker proves a known legacy post is absent from the homepage and feed,
  has `noindex,follow` and its own HTTPS canonical, and that representative
  month, tag, auxiliary, archive, 404, and compatibility pages are noindexed.
- The checker validates the generated Atom feed as XML and checks archive
  coverage for representative posts and all requested auxiliary sections.
- An isolated temporary current-era post with an explicit `/posts/<slug>/`
  permalink appears on the homepage and in the feed, stays out of `/archive/`,
  and is absent from the production source/output.
- `git diff --check` passes. No historical post body or legacy URL was changed.
