---
layout: default
permalink: /
---

{% include landing.html %}

{% for page in site.html_pages %}
    {{ page.title | sort: "weight"}}
    {{ page.weight }}
{% endfor %}
