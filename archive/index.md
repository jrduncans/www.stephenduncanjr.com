---
layout: default
title: Archive (2002–2018)
eyebrow: false
legacy: false
noindex: true
---

<p>Posts and other material from the previous version of this site, preserved
at their original URLs.</p>

<h2>Posts by year</h2>
{%- assign legacy_posts = site.posts | where_exp: "post", "post.era != 'current'" -%}
{%- assign posts_by_year = legacy_posts | group_by_exp: "post", "post.date | date: '%Y'" | sort: "name" | reverse -%}
{%- for year in posts_by_year -%}
  <h3>{{ year.name }}</h3>
  <ul class="link_list">
    {%- assign posts_by_title = year.items | sort: "title" -%}
    {%- for post in posts_by_title -%}
      <li><a class="internal" href="{{ post.url | relative_url }}">{{ post.title | smartify | escape }}</a></li>
    {%- endfor -%}
  </ul>
{%- endfor -%}

<h2>Writing</h2>
<ul class="link_list">
  <li><a class="internal" href="{% link writing/writing.html %}">Writing index</a></li>
  <li><a class="internal" href="{% link writing/poetry.html %}">Poetry</a></li>
  <li><a class="internal" href="{% link writing/prose.html %}">Prose</a></li>
  <li><a class="internal" href="{% link writing/other.html %}">Other writing</a></li>
  <li><a class="internal" href="{% link writing/allwriting.html %}">All writing</a></li>
</ul>

<h2>Pictures</h2>
<ul class="link_list">
  <li><a class="internal" href="{% link pictures.html %}">Pictures</a></li>
  <li><a class="internal" href="{% link thumbnailgallery.html %}">Thumbnail gallery</a></li>
</ul>

<h2>Projects</h2>
<ul class="link_list">
  <li><a class="internal" href="{% link projects.md %}">Projects</a></li>
</ul>

<h2>Firefox</h2>
<ul class="link_list">
  <li><a class="internal" href="{% link firefoxthemes.md %}">Firefox Themes</a></li>
</ul>

<h2>Research</h2>
<ul class="link_list">
  <li><a class="internal" href="{% link files/CooperDuncanGregg4.pdf %}">Cooper, Duncan, and Gregg research paper</a></li>
</ul>

<h2>Other preserved pages</h2>
<ul class="link_list">
  <li><a class="internal" href="{% link about.md %}">About Me</a></li>
</ul>
