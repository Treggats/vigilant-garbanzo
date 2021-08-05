---
layout: default
title: Articles
permalink: '/articles/'
---
{% assign articles = site.articles | sort: 'date' | reverse %}
{% for article in articles %}
   <h2><a href="{{ article.url }}">{{ article.title }}</a></h2>
   <p class="py-0 mb-1">{{ article.date | date: "%-d %B %Y" }}</p>
   <p class="mb-1">{{ article.excerpt }}</p>
{% endfor %}
