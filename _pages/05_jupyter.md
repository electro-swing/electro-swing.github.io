---
layout: default2
title: Jupyter
permalink: /jupyter/
---


Jupyters will be here.  
  
{% for jupyter in site.jupyter  %}
  <h2>
    <a href="{{ jupyter.url }}"> 
   {{ jupyter.title }} 
 </a>  
</h2>


{% endfor %}
