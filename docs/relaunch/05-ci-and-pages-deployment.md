# Phase 5: CI and GitHub Pages deployment

Status: complete

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

- Workflow: `.github/workflows/jekyll.yml`.
- Runtime pins: Ruby `4.0.6`, Bundler `4.0.19`, Jekyll `4.4.1` from the
  committed Gemfile and lockfile.
- Action pins: `actions/checkout` `v7.0.1`, `ruby/setup-ruby` `v1.321.0`,
  `actions/configure-pages` `v6.0.0`, `actions/upload-pages-artifact` `v5.0.0`,
  and `actions/deploy-pages` `v5.0.0`; every action is pinned to a full commit
  SHA with a version comment.
- Workflow behavior: pull requests and all pushes run the production build and
  `bundle exec bin/check-site`; artifact upload and deployment require a
  successful push to `refs/heads/main`. Deployment targets the `github-pages`
  environment and uses
  non-canceling `github-pages` concurrency. Build permissions are `contents: read`
  and `pages: read`; deployment permissions are `pages: write` and
  `id-token: write`.
- Pages URL handling: the main build writes an ephemeral runner-temp Jekyll
  config from `configure-pages` outputs for `url`/`baseurl`; the committed
  production config remains unchanged for local preview and the final custom
  domain.
- Local verification: `mise exec -- bundle install`, the pinned Ruby/Bundler/
  Jekyll version checks, a production build, `mise exec -- bundle exec bin/check-site`,
  and `mise exec -- git diff --check` passed. `bin/check-site`
  verified all 448 compatibility paths, current-post filtering, feed XML,
  legacy separation, validation failures, and Rouge fixture output. The check
  script uses standard `grep` assertions and does not require `rg` on the runner.
- Pages-base-path verification: an isolated build with
  `https://jrduncans.github.io` and `/www.stephenduncanjr.com` project settings passed
  canonical, CSS, feed, archive, and 404 checks. The fresh artifact measured
  4.0 MB across 452 files. The local checkout measured 8.9 MB and its Git object
  database 2.0 MB.
- Successful branch Actions run: commit `17203322c3c7519526992a0fcb046ab5e5a9636c`
  passed the production build and all site checks on the
  `blog-upgrade-and-reset` branch. As intended, its Pages artifact and deployment
  steps were skipped because it was not a push to `main`.
- Deployment-gate verification: the successful pull-request run
  `32618543480` and successful non-`main` push run `32618539542` skipped artifact
  upload and deployment. Both successful `main` runs `32551919463` and
  `32618573390` completed the build, artifact upload, and deployment jobs.
- Successful `main` deployments: run `32551919463` deployed commit
  `97f8328f5a5dcc0d29bfab9f2b5d8962603310bc`; the subsequent run
  `32618573390` deployed commit
  `8f87ab540590555e69c6ac3e7e34424e2f3933dc` and completed successfully. The
  second run is the verified update deployment.
- Temporary Pages URL: `https://jrduncans.github.io/www.stephenduncanjr.com/`.
  The homepage, current post, archive, representative legacy paths, compatibility
  feed path, and 404 page all returned HTTP 200. Canonical and `noindex` metadata
  matched the current/legacy rules, CSS loaded, and desktop plus 375x812 mobile
  rendering was checked without horizontal overflow.
- Feed verification: `/feed.xml` returned HTTP 200 as `application/xml`; the
  live Atom document parsed successfully, included the current post, and
  excluded the legacy `testing.html` post.
- Repository setting: Pages is public with `build_type: workflow`, source
  `main:/`, status `built`, HTTPS enforcement enabled, and no custom domain
  configured. The S3 origin remains available for rollback.
- AWS/DNS state: no DNS, Pages setting, AWS resource, credential, or S3 file was
  changed. `s3_website.yml` and the S3 origin remain available for rollback.
- Phase 6's technical prerequisites from this phase are satisfied; it remains
  pending DNS rollback-record capture and its user gates. No DNS, custom-domain,
  AWS resource, or S3 file was changed here.
