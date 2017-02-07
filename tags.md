---
layout: posts

permalink: /tags/
---

{% if site.posts != empty %} 
{% for tag in site.tags %} 	
<a href="{{ site.baseurl }}/tags/#{{ tag[0] }}" title="{{ tag[0] }}" rel="{{ tag[1].size }}">{{ tag[0]}}<span class="size">{{ tag[1].size }}</span></a>{% endfor %}

{% for tag in site.tags %}

<h2 id="{{tag[0]}}">{{ tag[0] }}</h2>


{% for post in tag[1] %} 

<span>{{ post.date | date:"%Y-%m-%d" }} » <a href="{{post.url}}">{{ post.title }}</a></span>

{% endfor %}


{% endfor %} 
{% else %} 
	No posts

{% endif %}