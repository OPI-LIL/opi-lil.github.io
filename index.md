---
layout: page
title: Natural Language Processing Laboratory
tagline: Cutting edge NLP research
---
{% include JB/setup %}

## Recent Posts
<ul class="posts">
  {% for post in site.posts %}
    <li>
		<span>{{ post.date | date_to_string }}</span> &raquo;
	 	<a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
	</li>
  {% endfor %}
</ul>
---

  
## About us
Natural Language Processing Laboratory fulfills assignments connected with natural language processing and text/web mining. Particular emphasis is put on semantic analysis of texts, information retrieval and recommendation engines.

The part of conducted work is also connected with multicriterial optimization problems using genetic algorithms. 


