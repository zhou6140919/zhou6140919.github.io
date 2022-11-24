---
layout: page
title: About
permalink: /about/
weight: 3
---

# **About Me**

Hi I am **{{ site.author.name }}** :wave:,<br>
I just got my bachelor's degree from Beijing University of Technology. My major is Electronic Information Engineering. 
At present, I am applying for the master's program in the United States, and my future research direction will be computer science. 
I'm interested in natural language processing.

<div class="row">
{% include about/skills.html title="Programming Skills" source=site.data.programming-skills %}
{% include about/skills.html title="Other Skills" source=site.data.other-skills %}
</div>

<div class="row">
{% include about/skills.html title="Language Skills" source=site.data.language-skills %}
{% include about/skills.html title="Hobbies" source=site.data.hobby %}
</div>

<div class="row">
{% include about/timeline.html %}
</div>

{% capture list_items %}
Outstanding Student Cadre 2020
14th IEEExtreme Programming Competition ranking 76/2155
International Exchange Scholarship BJUT 2021
Innovation and Entrepreneurship Award BJUT 2021
Academic Excellence Award BJUT 2021
International Exchange Scholarship FGH College 2021
{% endcapture %}
{% include elements/list.html title="Honors" type="toc" %}

{% capture carousel_images %}
../photos/me.JPG
../photos/cat.HEIC
{% endcapture %}
{% include elements/carousel.html %}
