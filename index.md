---
layout: default
title: "Home"
---
<p align=center>
Hello traveler! I'm a recent graduate in Automation Engineering with a lot of creativity and not enough time to work on all of my ongoing projects. This website is the place where I will hopefully document some of
them along with notable events from my career.  My interests are in hardware development, biomedical engineering, electric mobility, and machine learning. Hope you find whatever you're looking for!  
</p>

{% if site.show_excerpts %}
  {% include home.html %}
{% else %}
  {% include archive.html title="Posts" %}
{% endif %}
