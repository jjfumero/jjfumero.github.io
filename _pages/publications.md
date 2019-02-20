---
layout: archive
title: "\cite{publications}"
permalink: /publications/
author_profile: true
---

You can find the updated list of my articles on <u><a href="https://scholar.google.de/citations?user=Ig3N8j0AAAAJ&hl=en">my Google Scholar profile</a></u> and <u><a href="https://dl.acm.org/author_page.cfm?id=81548008457&coll=DL&dl=ACM&trk=0">ACM Digital Library</a></u>

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
