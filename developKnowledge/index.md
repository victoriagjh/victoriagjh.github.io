---
layout: default
title: "Develop Knowledge"
description: The history of Development Study
main: true
project-header: true
---

<ul class="catalogue">
{% assign sorted = site.pages | sort: 'order' | reverse %}
{% for page in sorted %}
{% if page.developKnowledge == true %}
{% include post-list.html %}
{% endif %}
{% endfor %}
</ul>
