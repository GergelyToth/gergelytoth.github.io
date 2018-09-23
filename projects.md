---
layout: page
title: Projects
permalink: /projects/
---

{% assign projects=site.projects %}
{% for project in projects %}

## {{ project.title }}

{% if project.website %} [Website]({{ project.website }}) {% endif %} {% if project.github %} [Github]({{ project.github }}) {% endif %}

{{ project.output }}

{% endfor %}
