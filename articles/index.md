---
layout: page
title: articles
---

<div style="text-align: right; margin-bottom: 0.25rem;">
  <a href="{{ "/feed.xml" | relative_url }}" target="_blank" style="color: #c00; text-decoration: none; font-size: 0.9em;">subscribe via RSS</a>
</div>

<div class="simplified-articles-list">
{% for post in site.posts %}
  <div class="simplified-article-item">
    <a href="{{ post.url | relative_url }}" class="simplified-article-title">- {{ post.title }}</a>
    <span class="simplified-article-date">{{ post.date | date: "%B %d, %Y" }}</span>
  </div>
{% endfor %}
</div>

{% if site.posts.size == 0 %}
<p>No articles available yet. Check back soon for the latest insights on the EO Framework.</p>
{% endif %}