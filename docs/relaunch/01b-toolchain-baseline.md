# Phase 1b: Current, repeatable toolchain baseline

Status: complete

## Objective

Replace the temporary Ruby 3.1/Jekyll 4.3 baseline with one current, explicitly
pinned toolchain before changing content behavior or layouts. Prove that the
new toolchain builds the same required site paths on macOS and Linux, then make
these versions authoritative for every later phase and the eventual Pages
workflow.

## Fixed versions and constraints

- Ruby: `4.0.6`
- Jekyll: `4.4.1`, resolved under a Gemfile constraint of `~> 4.4`
- Bundler: `4.0.19`
- Build timezone: `America/Los_Angeles`
- Jekyll major version: remain on 4; do not test or adopt an unreleased or
  released Jekyll 5 in this phase.

Use `.ruby-version` as the single repository runtime pin. The Gemfile should
read that file for its Ruby requirement rather than duplicate the version as an
independent literal. The lockfile remains the authority for exact gem versions.

## Exploratory evidence

Before this packet was written, a disposable copy of the repository was tested
on the local arm64 macOS host with Ruby 4.0.6, Jekyll 4.4.1, and Bundler 4.0.19.
`bin/check-site` completed successfully and verified all 449 paths in the Phase
1 fixture. This is evidence that the upgrade is viable, not a substitute for
the clean, cross-platform verification required below.

The exploratory update left the direct plugins at `jekyll-archives` 2.2.1 and
`jekyll-feed` 0.17.0 while updating Jekyll's transitive dependencies. No
temporary-copy changes are present in this worktree and they must not be copied
wholesale without review.

## Allowed repository changes

Keep this phase limited to:

- `.ruby-version`;
- `Gemfile` and `Gemfile.lock`;
- `_config.yml`;
- `test/fixtures/legacy-urls.txt`;
- `bin/check-site`, only if a change is required for Ruby 4, Bundler 4, or
  cross-platform correctness;
- this phase document and `docs/relaunch/README.md`;
- a short durable setup note only if one is necessary to make the commands
  discoverable before Phase 4 creates the full authoring documentation.

Do not change layouts, includes, post or page bodies, navigation, feed logic,
stylesheets, JavaScript, deployment workflows, DNS, or AWS resources.

## Scope

1. Add `.ruby-version` containing exactly `4.0.6` followed by a newline.
2. Make the Gemfile read the Ruby requirement from `.ruby-version`.
3. Constrain Jekyll to `~> 4.4` so ordinary dependency updates cannot silently
   cross the future Jekyll 5 major-version boundary.
4. Resolve and commit a fresh lockfile using Ruby 4.0.6 and Bundler 4.0.19.
5. Retain both the generic Ruby and `x86_64-linux` platforms needed by local and
   GitHub-hosted builds. Do not retain stale platform entries without a reason.
6. Confirm the lock resolves Jekyll 4.4.1 and compatible versions of
   `jekyll-feed` and `jekyll-archives`.
7. Correct `_config.yml` from `s3_website.yaml` to the actual filename,
   `s3_website.yml`. Jekyll's exclusion is a literal filename match; the old
   spelling currently publishes the S3 deployment configuration by accident.
8. Remove `/s3_website.yml` from the compatibility fixture. This is the one
   intentional output-path removal in this phase and reduces the required path
   count from 449 to 448.
9. Run the clean macOS and Linux verification below.
10. Record the final versions, commands, platform results, output count, and
    any non-semantic renderer differences in the handoff.

Do not prune the old `/scripts/*` assets in this phase. Phase 3 owns the evidence
and fixture adjustments for obsolete presentation/runtime assets.

## Dependency-update rules

- A normal transitive dependency update caused by Jekyll 4.4.1 is allowed.
- Do not add a theme, new Jekyll plugin, JavaScript toolchain, or system package
  to work around a build failure.
- Do not loosen plugin constraints beyond what their current releases declare.
- Do not hand-edit resolved dependency versions merely to force Bundler to
  accept an unavailable package. Resolve with Bundler under the pinned Ruby and
  required platforms.
- Review the final lockfile for unexpected sources, git dependencies, local
  paths, prereleases, or a Jekyll 5 dependency before accepting it.

## Required verification

### macOS

Use the pinned Ruby selected by the repository, not whichever Ruby happens to
be first on `PATH`:

```sh
ruby -v
bundle -v
bundle install
TZ=America/Los_Angeles JEKYLL_ENV=production bundle exec jekyll build
bin/check-site
git diff --check
```

The reported versions must be Ruby 4.0.6, Bundler 4.0.19, and Jekyll 4.4.1.
Also start `bundle exec jekyll serve` long enough to confirm that `/` returns a
successful HTML response, then stop the server cleanly. Do not leave a server
or background process running.

### Linux

Repeat dependency installation and `bin/check-site` in a disposable Linux
environment using Ruby 4.0.6. A Docker container is acceptable. Mount the
repository read-only and copy it inside the disposable container before
installing so the test cannot create root-owned artifacts in the worktree.

The Linux test must:

- install Bundler 4.0.19;
- install solely from the committed Gemfile and lockfile;
- report Jekyll 4.4.1;
- build with `TZ=America/Los_Angeles` and `JEKYLL_ENV=production`;
- pass `bin/check-site` for all 448 required paths;
- leave no container-generated files in the repository.

If Docker is unavailable, another clean x86_64 Linux environment is acceptable.
Do not treat the old Ruby 3.1 container result as the Linux verification for
this new toolchain.

## Compatibility review

In addition to path existence, inspect representative rendered output for:

- an early HTML post;
- a Markdown post with Rouge-highlighted code;
- a month archive and tag archive;
- the feed;
- CSS and its source map;
- local images and the PDF.

Renderer version metadata, feed build timestamps, source-map internals, and
Rouge token-class changes may differ. Record them if observed. Any changed post
text, missing content, moved URL, broken internal link, or altered publication
date is a stop condition rather than an accepted dependency-update side effect.

The checked-in `_site` directory was previously built with a GitHub project
base path and is not a byte-for-byte oracle. Compare meaningfully generated
content and required paths, not timestamps or the stale `/jrduncans` prefix.

## Stop conditions

Stop and report instead of broadening the phase if:

- any required path other than `/s3_website.yml` disappears or moves;
- a post date or month assignment changes;
- Jekyll 5 or a prerelease enters the dependency graph;
- either existing direct plugin is incompatible with Jekyll 4.4.1;
- clean installation requires editing content, layouts, or deployment code;
- the macOS and Linux builds resolve materially different unsupported graphs;
- Ruby 4.0.6, Bundler 4.0.19, or Jekyll 4.4.1 cannot complete the required
  build without an undocumented manual gem installation;
- historical rendered content changes in a way that cannot be explained as
  non-semantic generator or syntax-token markup variation.

## Acceptance criteria

- The repository itself selects Ruby 4.0.6.
- The Gemfile prevents an accidental Jekyll 5 upgrade.
- The lockfile records Bundler 4.0.19, Jekyll 4.4.1, and the required platforms.
- `bundle install` works from the committed files without ad hoc gem fixes.
- Fresh macOS and Linux builds pass `bin/check-site` with 448 required paths.
- The generated site no longer contains `/s3_website.yml`.
- No other required URL is removed or moved.
- Representative content review finds no semantic historical-content change.
- The worktree contains no container artifacts or unrelated edits.
- Phase 1b's handoff is complete, Phase 1b is marked complete, and Phase 2 is
  marked ready.

## Out of scope

- Jekyll 5 exploration or migration.
- Changing the site design or content model.
- Removing old scripts or mixed-content resources.
- Replacing the GitHub Actions workflow; Phase 5 must consume this runtime pin.
- Adding Dependabot or automated dependency updates.
- Editing historical content.
- Changing public hosting, DNS, or AWS.

## Handoff record

- Toolchain: Ruby 4.0.6 from `.ruby-version`, Bundler 4.0.19, Jekyll 4.4.1,
  `jekyll-feed` 0.17.0, `jekyll-archives` 2.3.0, `jekyll-sass-converter`
  3.1.0, `kramdown` 2.5.2, and Rouge 4.7.0. The lockfile retains `ruby` and
  `x86_64-linux` platforms and contains no git, local-path, prerelease, or
  Jekyll 5 dependency.
- Changed files: `.ruby-version`, `Gemfile`, `Gemfile.lock`, `_config.yml`,
  `test/fixtures/legacy-urls.txt`, this handoff, `docs/relaunch/README.md`,
  and the Phase 2 status line. The compatibility fixture contains 448 paths.
- macOS: `mise exec -- bundle install` completed from the committed files.
  `ruby -v` reported Ruby 4.0.6, `bundle -v` reported Bundler 4.0.19,
  `bundle exec jekyll -v` reported Jekyll 4.4.1, the production build passed,
  `bin/check-site` verified 448 paths, and `git diff --check` passed. The
  serve smoke test returned HTTP 200 for `/` and was stopped cleanly.
- Linux: disposable `ruby:4.0.6-bookworm` with `--platform linux/amd64`,
  repository mounted read-only and copied into the container. Bundler 4.0.19
  installed solely from the committed Gemfile and lockfile; Jekyll 4.4.1
  built with `TZ=America/Los_Angeles` and `JEKYLL_ENV=production`, and
  `bin/check-site` verified 448 paths. No container artifacts were written to
  the repository.
- Representative review: the early HTML post, Markdown post with Rouge
  highlighting, March 2009 month archive, programming tag archive, feed, CSS
  and source map, local PNG, and PDF all rendered and remained present. Dates,
  month assignment, titles, post text, and URLs were unchanged. Accepted
  non-semantic differences are Jekyll 4.4.1 generator metadata, the build-time
  feed timestamp, source-map internals, and Rouge's generated token markup.
- `_site/s3_website.yml` is absent; the other 448 fixture paths remain. The
  worktree contains no container-generated files. No commit or pull request
  was created.
