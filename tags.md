---
layout: page
slug: tags
title: Tags
cover: http://assets.boringgeek.com/imacSceneryReduced.jpg
---

{% capture tags %}
  {% for tag in site.tags %}
    {{ tag[0] }}
  {% endfor %}
{% endcapture %}
{% assign sortedtags = tags | split:' ' | sort %}

{% for tag in sortedtags %}
  <h4 id="{{ tag }}">{{ tag }}</h4>
  <ul>
  {% for post in site.tags[tag] %}
  <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  </ul>
{% endfor %}
