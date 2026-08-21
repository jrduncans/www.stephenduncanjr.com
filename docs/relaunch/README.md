# Blog relaunch execution plan

Status: approved for implementation

This directory is temporary execution scaffolding for the relaunch of
`www.stephenduncanjr.com`. Work through the phase documents in order. When the
relaunch is complete, move any lasting operational instructions into the main
project README and remove this directory.

## Outcome

Relaunch the existing Jekyll blog with a modern, text-first design and a simple
Markdown/commit/push publishing workflow. Host it for free on GitHub Pages with
HTTPS. Keep every existing individual post URL working while moving all
pre-relaunch material into an unobtrusive, non-indexed archive.

## Fixed decisions

- Keep Jekyll 4. A generator migration is a separate future project.
- Deploy the public repository with GitHub Actions and GitHub Pages.
- Keep `www.stephenduncanjr.com` canonical.
- Redirect `stephenduncanjr.com` to `www.stephenduncanjr.com`.
- Redirect HTTP to HTTPS after certificate provisioning.
- Treat every existing post and site page as legacy content.
- Mark legacy pages and the legacy archive `noindex,follow`.
- Keep legacy content out of the current homepage, feed, and sitemap.
- Put one subdued legacy-archive link in the site footer.
- Author new posts locally as Markdown, then commit and push.
- Use Rouge for syntax highlighting without a client-side highlighting library.
- Do not add analytics, comments, newsletters, sharing controls, or third-party
  runtime JavaScript.
- Do not require a hero or preview image for any post.
- Use `Stephen Duncan Jr.` as the provisional site title.
- Do not publish a relaunch announcement. The first current-era post must be
  substantive.
- Leave `jrduncans.com` out of scope.

## Content model

New posts carry an explicit `era: current` front-matter value. Existing posts
do not need bulk edits: a post without that value is legacy. Current posts use
an explicit, stable `/posts/<slug>/` permalink. Existing dated `.html` URLs
remain unchanged.

Legacy non-post pages should be classified with the smallest maintainable
mechanism appropriate to their layouts or paths. Do not alter historical prose
merely to modernize formatting or opinions.

## Target public surface

| URL | Purpose | Indexed? |
| --- | --- | --- |
| `/` | Current-era post list | Yes |
| `/posts/<slug>/` | Current post | Yes |
| `/feed.xml` | Current-era feed only | N/A |
| `/archive/` | Previous site, grouped and quietly linked | No |
| Existing dated URLs | Preserved historical posts | No |
| Existing auxiliary URLs | Preserved previous-site content | No |
| `/404.html` | Useful error page | No |

## Invariants

Every implementation phase must preserve these properties:

1. Required legacy URLs still produce a page at the same path.
2. Historical post bodies are not rewritten as part of the relaunch.
3. A current post cannot silently fall into the legacy archive.
4. Legacy material cannot enter the current homepage, feed, or sitemap.
5. No deployment occurs from a pull request or non-`main` branch.
6. No external DNS, GitHub Pages setting, or AWS resource is changed without a
   clearly identified user gate.
7. The existing S3 site remains available as rollback protection through the
   DNS cutover observation period.

## Phase sequence

| Phase | Document | Status | Depends on |
| --- | --- | --- | --- |
| 1 | [Baseline and URL compatibility](01-baseline-and-url-compatibility.md) | Complete | None |
| 1b | [Current, repeatable toolchain baseline](01b-toolchain-baseline.md) | Complete | Phase 1 |
| 2 | [Legacy content separation](02-legacy-content-separation.md) | Complete | Phase 1b |
| 3 | [Modern design](03-modern-design.md) | Complete | Phase 2 |
| 4 | [Authoring workflow](04-authoring-workflow.md) | Complete | Phase 2 |
| 5 | [CI and Pages deployment](05-ci-and-pages-deployment.md) | Ready | Phases 1-4 |
| 6 | [Domain cutover and cleanup](06-domain-cutover-and-cleanup.md) | Blocked | Phase 5 and a substantive post |

Phase 1b must establish the toolchain used by every later phase. Phases 3 and 4
may be implemented independently after Phase 2. Phase 5 should integrate both
before deployment and configure GitHub Actions from the Phase 1b runtime pin.

## Instructions for an implementation agent

1. Read this file and the assigned phase completely.
2. Inspect the current worktree before editing and preserve unrelated changes.
3. Stay within the assigned phase; record future work instead of implementing
   it early.
4. Prefer a small, reviewable implementation over a framework or dependency
   expansion.
5. Run every verification command available for the phase.
6. Update the status table and the phase's handoff section with facts, not a
   narrative transcript.
7. Stop at user gates. Do not modify DNS, Pages settings, or AWS merely because
   the repository work is ready.

## Global non-goals

- Migrating to Astro, Eleventy, Hugo, or another generator.
- Building a portfolio or identity site at `jrduncans.com`.
- Editorially reviewing or repairing 276 historical posts.
- Fixing every dead historical outbound link.
- Automating LinkedIn syndication.
- Adding a CMS, database, server runtime, or paid service.
- Deleting the S3 bucket during repository implementation.

## Completion

The relaunch is complete when all phase acceptance criteria pass, the first
substantive current-era post is live, the domain matrix works over HTTPS,
representative legacy URLs still work, and the observation period has passed.
Before removing these temporary files, preserve the local authoring and recovery
instructions in permanent project documentation.
