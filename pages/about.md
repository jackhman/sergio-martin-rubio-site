---
layout: page
title: About
permalink: /about/
weight: 4
---

# **About Me**

Hi I am **{{ site.author.name }}** :wave: ,<br>
a passionate developer who loves challenges and is never afraid of diving into new territories. I enjoy sharing my knowledge with others and get inspired by them.

<div class="row">
{% include about/skills.html title="Programming Skills" source=site.data.programming-skills %}
{% include about/skills.html title="Other Skills" source=site.data.other-skills %}
</div>

<div class="row">
{% include about/timeline.html %}
</div>