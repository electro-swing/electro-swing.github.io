---
layout: default2
title: Work
permalink: /work/
---


These things are work related. 
  
{% for work in site.work  %}
  <h2>
    <a href="{{ work.url }}">
      {{ work.title }} - {{ work.position }}
    </a>
  </h2>


{% endfor %}