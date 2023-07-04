---
title: "develop"
layout: archive
permalink: /develop
---


{% assign posts = site.categories.develop %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
