# Phase 4: Authoring workflow

Status: ready

## Objective

Make a new technical post easy to create, preview, validate, commit, and publish
without a CMS. The happy path is local Markdown followed by a push to `main`.

## Current-post contract

A publishable current post must have:

- a filename and date acceptable to Jekyll;
- `layout: post` if not supplied by a safe default;
- `title`;
- `date` with timezone;
- a short `description` suitable for the homepage and link metadata;
- `era: current`;
- an explicit, stable `permalink: /posts/<slug>/`;
- Markdown content using fenced code blocks when appropriate.

Tags may be supported but must remain optional. A title edit must not silently
change an already published permalink.

## Scope

1. Add a small repository-native new-post command. It should accept a title,
   create a conservative slug, refuse to overwrite a file, and produce valid
   front matter.
2. Support drafts using Jekyll's `_drafts` convention or a clearly documented
   equivalent. Publishing a draft must result in the current-post contract
   above.
3. Add concise permanent documentation for:
   - installing the locked Ruby dependencies;
   - starting a local preview with drafts;
   - creating a post;
   - previewing and checking it;
   - publishing by commit and push;
   - correcting a published post without changing its URL.
4. Add validation that fails clearly when a current post lacks required fields,
   uses a duplicate permalink, inherits a legacy URL, or collides with an
   existing output path.
5. Demonstrate syntax highlighting using temporary fixture or draft content,
   not a fake published post.

Keep the script dependency-free beyond the Ruby/Jekyll environment already
required by the repository. Avoid introducing Node solely for post creation.

## Suggested commands

Exact names may vary, but the finished workflow should be comparable to:

```sh
bundle install
bin/new-post "A durable title"
bundle exec jekyll serve --drafts --livereload
bin/check-site
```

If draft publication needs a separate command, document it adjacent to
`bin/new-post` and make its behavior safe and deterministic.

## Author checklist

Permanent documentation should include this short checklist:

1. Title and description accurately describe the post.
2. The permalink is intentional and stable.
3. Code fences name the language where useful.
4. Links and local media work in preview.
5. No secret, credential, private URL, or sensitive screenshot is present.
6. The production build and site check pass.
7. Publish the owned blog version before or alongside any LinkedIn adaptation.

Do not automate LinkedIn in this phase.

## Acceptance criteria

- A new draft can be created and previewed with one documented flow.
- A draft can become a valid current-era post without hand-assembling all front
  matter.
- The command handles punctuation, repeated whitespace, and slug collisions
  safely.
- Validation catches a missing `era`, description, or explicit permalink.
- No sample relaunch post is published.
- The standard build and compatibility checks pass.

## Out of scope

- A browser CMS or GitHub web editor workflow.
- Scheduled publishing.
- LinkedIn API integration.
- Automatic post images.
- A generator migration.

## Handoff record

When complete, record the commands, generated front-matter schema, validation
location, draft-to-publish flow, and verification results here. Mark Phase 4
complete in `README.md`.
