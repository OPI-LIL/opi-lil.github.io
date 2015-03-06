---
layout: page
title: Categories
permalink: /categories/
feature-img: "img/sample_feature_img.png"
---


{% capture site_categories %}{% for category in site.categories %}{{ category | first }}{% unless forloop.last %},{% endunless %}{% endfor %}{% endcapture %}
{% assign category_words = site_categories | split:',' | sort %}

<div id="categories">
  <h3>Categories</h3>
  <hr>
	  <ul class="tag-box inline">
	  {% for item in (0..site.categories.size) %}{% unless forloop.last %}
	    {% capture this_word %}{{ category_words[item] | strip_newlines }}{% endcapture %}
	    <a href="#{{ this_word | cgi_escape }}">{{ this_word }} </a> (<span>{{ site.categories[this_word].size }}</span>)
	  {% endunless %}{% endfor %}
	  </ul>
  <hr>

  {% for item in (0..site.categories.size) %}{% unless forloop.last %}
    {% capture this_word %}{{ category_words[item] | strip_newlines }}{% endcapture %}
  <h2 id="{{ this_word | cgi_escape }}">{{ this_word }}</h2>
  <ul class="posts">
    {% for post in site.categories[this_word] %}{% if post.title != null %}
    <li itemscope><span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}" itemprop="datePublished">{{ post.date | date: "%B %d, %Y" }}</time></span> &raquo; <a href="{{ post.url }}">{{ post.title | capitalize  }}</a></li>
    {% endif %}{% endfor %}
  </ul>
  {% endunless %}{% endfor %}
</div>