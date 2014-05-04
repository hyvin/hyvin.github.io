---
layout: page
title: "categories"
comments: false
sharing: true
footer: false
---
<ul>
{% for item in site.categories %}
    <li><span class="categories"><a class = "category" href="/blog/categories/{{ item[0] }}/">{{ item[0]}}</a></span> [{{ item[1].size }}]</li>
{% endfor %}
</ul>