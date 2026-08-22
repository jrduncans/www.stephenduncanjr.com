# Stephen Duncan Jr.

This is a Jekyll site. New technical posts are written locally in Markdown and
published by committing and pushing to `main`.

## Authoring a post

Install the repository's locked Ruby dependencies with:

```sh
bundle install
```

Create a draft from a title. The command makes a conservative stable slug,
will not overwrite a draft, and also refuses a slug already used by a published
post:

```sh
bin/new-post "A durable title"
```

Edit the resulting `_drafts/a-durable-title.md`. Add a concise `description`
before publishing; the generated permalink is intentionally stable. Fenced
code blocks can name a language and are highlighted with Rouge:

````markdown
```ruby
puts "Hello, world!"
```
````

Preview the site, including drafts, at the local address Jekyll prints:

```sh
bundle exec jekyll serve --drafts --livereload
```

When the draft is ready, publish it using its slug. This adds a timezone-aware
publication date, moves it into `_posts/`, and preserves the explicit
`/posts/<slug>/` permalink:

```sh
bin/publish-post a-durable-title
```

The published front matter schema is:

```yaml
layout: post
title: A durable title
date: 2026-08-21 09:00:00 -0700
description: A concise summary for the homepage and link metadata.
era: current
permalink: /posts/a-durable-title/
```

Tags are optional. Check the production build and compatibility rules before
committing, then publish by pushing the owned blog version to `main`:

```sh
mise exec -- bundle exec bin/check-site
git add _posts README.md
git commit -m "Publish a durable title"
git push origin main
```

To correct a published post, edit its Markdown file in `_posts/`, preview and
run `mise exec -- bundle exec bin/check-site` again. Keep its `permalink`
unchanged—even when its title changes—so existing links continue to work. Do
not automate or publish a LinkedIn adaptation before or instead of the owned
blog version.

## Author checklist

1. Title and description accurately describe the post.
2. The permalink is intentional and stable.
3. Code fences name the language where useful.
4. Links and local media work in preview.
5. No secret, credential, private URL, or sensitive screenshot is present.
6. The production build and site check pass.
7. Publish the owned blog version before or alongside any LinkedIn adaptation.
