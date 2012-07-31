---
layout: page
title: Latest blogs
tagline: Hello World!
---
{% include JB/setup %}

{% for post in site.posts %}
{{ post.date | date_to_string }} >> {{ post.title }}
{% endfor %}
