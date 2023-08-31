---
layout: default
title: "Home"
---

Hello traveller!!!

My name is Maro Michailidou, an Automation Engineer with a lot of creativity and not enough time for all of my projects. 

This is the place where I document some of my projects. Hope you find whatever you're looking for. 


{% if site.show_excerpts %}
  {% include home.html %}
{% else %}
  {% include archive.html title="Posts" %}
{% endif %}
