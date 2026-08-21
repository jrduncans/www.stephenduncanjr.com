# Phase 5: CI and GitHub Pages deployment

Status: ready

## Objective

Replace the abandoned Pages experiment with a fresh, current GitHub Actions
workflow that validates every change and deploys only `main`. Prove the site on
GitHub Pages before changing public DNS.

## Workflow policy

- Pull requests and pushes run the production build and all local verification.
- Only a successful push to `main` may deploy.
- Use the official GitHub Pages artifact/deployment flow.
- Use current stable action versions at implementation time and pin actions to
  full commit SHAs with a version comment.
- Grant the minimum required permissions: read repository contents during the
  build and Pages/id-token write permissions only for deployment.
- Use the `github-pages` environment and deployment concurrency controls.
- Do not store AWS credentials or add a second deployment target.
- Do not deploy generated `_site` files by committing them to `main`.

## Scope

1. Remove or replace `.github/workflows/jekyll.yml`; no behavior in the existing
   workflow needs preservation.
2. Pin the supported Ruby version explicitly and use the lockfile.
3. Run the same build and checks documented for local use.
4. Upload the fresh generated artifact and deploy it with GitHub Pages.
5. Configure production `url`/`baseurl` behavior so local preview, the temporary
   Pages address, and the final custom domain do not produce broken asset links.
6. Confirm the public repository and generated site are comfortably within
   GitHub Pages limits.
7. Remove S3 deployment configuration from the active workflow and documentation
   once Pages deployment succeeds. Do not delete the S3 bucket.

## User gate: repository settings

An administrator must enable GitHub Pages with GitHub Actions as its source if
it is not already enabled. The implementation agent may prepare the workflow
and report the exact setting required, but must not claim deployment success
until the Pages deployment is visible.

Do not configure the custom domain in this phase unless needed for certificate
preparation and explicitly approved. Phase 6 owns the public-domain cutover.

## Pre-DNS verification

On the temporary Pages URL, verify:

- homepage and assets;
- current post filtering with non-production fixture content if no substantive
  post exists yet;
- `/archive/` and representative legacy paths;
- feed XML;
- canonical and robots metadata expectations;
- mobile and desktop rendering;
- 404 behavior to the extent supported by the temporary URL;
- a subsequent push updates the deployment.

Remove all fixtures before declaring the phase complete.

## Acceptance criteria

- CI passes from a clean checkout.
- Pull requests cannot deploy.
- A successful `main` build deploys through the official Pages mechanism.
- A failed check prevents deployment.
- The temporary Pages site is visually and functionally verified.
- No AWS credential or deployment step remains in the active workflow.
- The S3 origin remains untouched and available for rollback.

## Out of scope

- Namecheap changes.
- Enabling final HTTPS enforcement.
- Deleting or emptying S3 resources.
- Publishing a fake post to make the homepage nonempty.

## Handoff record

When complete, record workflow paths, pinned runtime versions, the successful
Actions run, temporary Pages URL, repository setting changed by the user,
verification results, and any configuration required before Phase 6. Mark Phase
5 complete and Phase 6 ready only when a substantive current post is also ready
for launch.
