---
layout: default
title: 我的Blog
---


# a header

I am index.md
<p><br /><b>最新文章:</b></p>

<ul class="posts">
	{% for post in site.posts %}
		<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
	{% endfor %}
</ul>
