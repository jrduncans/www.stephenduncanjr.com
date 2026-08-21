# Phase 1: Baseline and URL compatibility

Status: complete

## Objective

Create a repeatable baseline build and an automated compatibility guard before
changing layouts, navigation, permalinks, or deployment. This phase answers
"what must continue to exist?" without redesigning the site.

## Known starting point

- Jekyll 4.3.2 is locked in the current bundle.
- The repository contains 276 posts and 24 files under `writing/writings`.
- Post permalinks follow `/:year/:month/:title.html`.
- The checked-in `_site` directory is not authoritative evidence of a fresh
  build.
- Eight posts contain image markup.
- Seven historical content files contain script, iframe, object, or embed-style
  markup.
- The shared layout loads obsolete Google JavaScript and post pages load
  Disqus. Removing those belongs to Phase 3.

## Scope

1. Make a fresh production Jekyll build from the current source.
2. Record the build command and supported Ruby/Bundler assumptions.
3. Generate and commit a sorted manifest of legacy output paths that must
   survive later phases.
4. Add a deterministic verification command that builds the site and fails if
   a required legacy path is absent.
5. Classify active mixed-content risks separately from ordinary `http://`
   hyperlinks. Scripts, stylesheets, images, iframes, forms, and embeds are
   active risks; an old outbound anchor is not.
6. Record a short set of representative manual-check URLs covering:
   - early, middle, and late posts;
   - Markdown and HTML source posts;
   - posts with local and remote images;
   - month and tag archives;
   - About, writing, pictures, projects, Firefox, and the PDF;
   - the feed and 404 page;
   - old Blogger compatibility paths represented by `s3_website.yml`.

## Compatibility policy

- Every individual legacy post URL is required.
- Existing auxiliary content URLs are required unless the phase documents a
  concrete reason they cannot be served statically.
- Existing month and tag archive paths should remain valid and later become
  non-indexed.
- `/feeds/posts/default` should continue to reach the feed.
- The old dynamic `search/labels/<tag>` routing rule is best-effort; preserve
  known label paths if this is straightforward, but do not introduce a server
  runtime solely for wildcard routing.
- Exact visual output, Disqus comments, Google search, and remote third-party
  widgets are not compatibility requirements.
- Dead outbound websites are not build failures.

## Suggested durable artifacts

Names may be adjusted to fit the implementation, but the result should include:

- a committed legacy-path fixture, such as
  `test/fixtures/legacy-urls.txt`;
- one top-level verification entry point, such as `bin/check-site`;
- a brief record of intentionally unsupported historical routes.

Do not include generated HTML bodies in the fixture. Store paths only so the
guard remains reviewable.

## Verification

At minimum, the phase must make these operations pass:

```sh
bundle exec jekyll build
bin/check-site
```

The verification must use a fresh destination, not trust an old `_site`
directory, and clearly identify every missing required path.

## Acceptance criteria

- A clean checkout can build with documented commands.
- The legacy-path manifest is derived from a fresh baseline and is sorted and
  reviewable.
- Removing a representative legacy output causes the compatibility check to
  fail.
- The active mixed-content inventory distinguishes global template problems
  from isolated historical content.
- No public URL, layout, content body, navigation, feed, or deployment workflow
  has intentionally changed in this phase.

## Out of scope

- Changing the visual design.
- Adding `noindex`.
- Filtering the homepage or feed.
- Fixing mixed content.
- Replacing the GitHub Actions workflow.
- Changing DNS or GitHub Pages settings.

## Handoff record

Phase 1 is complete in the working tree. The baseline uses the existing Jekyll
4 stack and does not change layouts, content bodies, navigation, feed output,
or deployment settings.

### Build assumptions and command

- Ruby: 3.1.x, matching the existing GitHub Actions workflow.
- Bundler: 2.3.12, recorded in `Gemfile.lock`.
- Jekyll: 4.3.2, recorded in `Gemfile.lock`.
- Build timezone: `America/Los_Angeles`. Jekyll normalizes front-matter dates
  to the process timezone, and this preserves the existing month-boundary
  post URLs from the checked-in baseline.
- Install dependencies with `bundle install`.
- Build with `TZ=America/Los_Angeles bundle exec jekyll build`.
- The baseline build was run with `JEKYLL_ENV=production` and a fresh
  destination. The compatibility checker repeats that fresh build on every
  invocation.
- The lockfile now uses the available platform-neutral `google-protobuf`
  package instead of removed native package entries; Jekyll and plugin
  versions are unchanged.

The relaunch planning files and verification fixture are excluded from the
generated site. This keeps execution scaffolding from becoming public content.

### Compatibility artifacts and counts

- URL fixture: `test/fixtures/legacy-urls.txt`
- Verification entry point: `bin/check-site`
- Fresh baseline output paths: 449 total
- Individual post paths: 276
- Month archive paths: 57
- Tag archive paths: 22
- Other auxiliary/static paths: 94

The fixture contains URL paths only. Directory index files are represented by
trailing-slash URLs, and the root index is represented by `/`.

### Active mixed-content inventory

The inventory distinguishes active requests from ordinary historical links:

- Global/template risks: `_layouts/base.html:15` loads Google JavaScript over
  `http://`, and `_includes/navigation.html:27` submits the search form to an
  `http://` Google endpoint. `_config.yml:5` also still declares an HTTP site
  URL; this affects generated absolute URLs but is not itself a browser
  resource request.
- Isolated page risks: `error.md:13` loads the old Google fix-up script over
  `http://`; `pictures.html:26` loads a Flickr badge script over `http://`;
  and `thumbnailgallery.html` contains HTTP Flickr image sources.
- Historical post risks: eight posts contain image markup. The local-image
  example is `_posts/2005-01-21-testing-picasahello.html`; the other image
  markup references remote HTTP images, including
  `_posts/2006-02-07-gmail-google-talk.md`. The historical
  `_posts/2009-03-15-jquery-for-archives.md` also contains the obsolete Google
  script and inline JavaScript.
- A source scan of this checkout found no historical `iframe`, `object`, or
  `embed` tags and found the concrete historical script occurrences in the
  files named above; the older seven-file estimate was not reproducible from
  the current source.
- Separate runtime debt: the shared layouts still reference Disqus and the
  old Google JavaScript path. These are intentionally left for Phase 3.
- Ordinary outbound `http://` anchors in historical prose, writing pages, and
  image galleries are not active mixed-content failures and are not build
  failures. JavaScript bookmarklet links and the Firefox update metadata are
  recorded as historical content, not treated as page resource requests.

### Representative manual checks

Check these against the fresh output or a local static server:

- Early HTML post: `/2002/03/awards-shows.html`
- Middle Markdown post: `/2008/06/unmaintained-web-applications.html`
- Late Markdown post: `/2018/06/really-overdue.html`
- Local-image post: `/2005/01/testing-picasahello.html`
- Remote-image post: `/2006/02/gmail-google-talk.html`
- Month archive: `/2005/04/`
- Tag archive: `/tag/programming/`
- Auxiliary pages: `/about.html`, `/writing/writing.html`, `/pictures.html`,
  `/projects.html`, `/firefoxthemes.html`
- PDF: `/files/CooperDuncanGregg4.pdf`
- Feed: `/feed.xml`
- Current custom error document: `/error.html`

### Intentional route exceptions

- `/feeds/posts/default` is not emitted as a static file. The existing
  `s3_website.yml` records its redirect to `/feed.xml`; reproducing that
  redirect on GitHub Pages belongs to the deployment phase.
- `/search/labels/<tag>` remains best-effort. Known `/tag/<tag>/` archive paths
  are in the fixture, but this phase does not add a server runtime for a
  wildcard Blogger route.
- The current source emits `/error.html`, matching `error_document` in
  `s3_website.yml`; `/404.html` is not a baseline output and is deferred to
  the later design/deployment work.

### Verification results

- `TZ=America/Los_Angeles bundle exec jekyll build`: passes in the documented
  Ruby 3.1/Bundler environment; the fresh baseline produced 449 files.
- `bin/check-site`: passes and verifies all 449 fixture paths after rebuilding
  into a new temporary destination.

Phase 2 is ready. No commit or pull request was created by this implementation.
