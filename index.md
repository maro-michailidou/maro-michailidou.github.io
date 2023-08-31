---
layout: default
title: "Home"
---
> Hello traveler!!!  I'm a recent graduate in Automation Engineering
> with a lot of creativity and not enough time to work on all of my
> projects. This website is the place where I hopefully document some of
> them along with events from my professional career.  My interests are
> in hardware development, biomedical engineering, electric mobility,
> and machine learning.  Hope you find whatever you're looking for!  
> :D

{% if site.show_excerpts %}
  {% include home.html %}
{% else %}
  {% include archive.html title="Posts" %}
{% endif %}
