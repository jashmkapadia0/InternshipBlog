---
layout: default
title: Blog
description: Project notes and research updates.
permalink: /blog/
---
<section class="page-hero"><p class="eyebrow">04 / FIELD NOTES</p><h1>Notes from the research process.</h1><p class="lede">Short, dated entries on methods, implementation choices, and what the evidence does—and does not—show.</p></section>
<section class="section post-list"><p class="eyebrow">PROJECT NOTES</p>{% assign posts = site.posts | sort: 'date' | reverse %}{% if posts.size > 0 %}{% for post in posts %}<article class="post-card"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%d %b %Y" }}</time><div><h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2><p>{{ post.excerpt | strip_html | truncate: 220 }}</p><a class="arrow-link" href="{{ post.url | relative_url }}">Read note <span>→</span></a></div></article>{% endfor %}{% else %}<p>No project notes have been published yet.</p>{% endif %}</section>
