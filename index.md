---
layout: default
title: 我的Blog
---
<h2>{{ page.title }}</h2>

# a header

I am index.md markdown
<p><br /><b>最新文章:</b></p>

<ul class="posts">
	{% for post in site.posts %}
		<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.baseurl }}{{ post.url }}">{{ post.title }}</a>
	{% endfor %}
</ul>
