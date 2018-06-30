---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

You can find the updated list of my articles on <u><a href="https://scholar.google.de/citations?user=Ig3N8j0AAAAJ&hl=en">my Google Scholar profile</a>.</u>

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
