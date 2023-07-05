---
layout: pages
permalink: jekyll
title: "Jekyll"

author_profile: true
sidebar:
  nav: "docs"
---

{% assign posts = site.categories.jekyll %}
{% for post in posts %}
  <h2 class="archive__item-title no_toc" itemprop="headline">
    {% if post.link %}
      <a href="{{ post.link }}">{{ title }}</a> <a href="{{ post.url | relative_url }}" rel="permalink"><i class="fas fa-link" aria-hidden="true" title="permalink"></i><span class="sr-only">Permalink</span></a>
    {% else %}
      <a href="{{ post.url | relative_url }}" rel="permalink">{{ title }}</a>
    {% endif %}
  </h2>
{% endfor %}
