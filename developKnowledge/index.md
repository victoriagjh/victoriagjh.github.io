---
layout: default
title: "Develop Knowledge"
description: The history of Development Study
main: true
project-header: true
---
{% for dev in site.category-list %}
### {{ dev }}
<ul>
  {% for page in site.pages %}
    {% if page.categories contains 'devKnowledge'%}
    <div class="item">
    <h3>{{page.title}}</h3>
    <p>{{page.description}}</p>  
    <li><span><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a><time class="pull-right post-list">{{ post.date | date_to_string | date: "%b %-d, %Y"  }}</h4></time></span></span></li>
    </div>
    {% endif %}   <!-- resource-p -->
  {% endfor %}  <!-- page -->
</ul>
{% endfor %}  <!-- cat -->
