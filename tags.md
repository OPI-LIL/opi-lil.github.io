---
layout: page
title: Tags
permalink: /tags/
feature-img: "img/sample_feature_img.png"
---


{% capture site_tags %}{% for tag in site.tags %}{{ tag | first }}{% unless forloop.last %},{% endunless %}{% endfor %}{% endcapture %}
{% assign tag_words = site_tags | split:',' | sort %}

<div id="tags">
  <h3>Most popular tags</h3>
  <hr>
	  <ul class="tag-box inline">
	  {% for item in (0..site.tags.size) %}{% unless forloop.last %}
	    {% capture this_word %}{{ tag_words[item] | strip_newlines }}{% endcapture %}
	    <a href="#{{ this_word | cgi_escape }}">{{ this_word }} </a> (<span>{{ site.tags[this_word].size }}</span>)
	  {% endunless %}{% endfor %}
	  </ul>
  <hr>

  {% for item in (0..site.tags.size) %}{% unless forloop.last %}
    {% capture this_word %}{{ tag_words[item] | strip_newlines }}{% endcapture %}
  <h2 id="{{ this_word | cgi_escape }}">{{ this_word }}</h2>
  <ul class="posts">
    {% for post in site.tags[this_word] %}{% if post.title != null %}
    <li itemscope><span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}" itemprop="datePublished">{{ post.date | date: "%B %d, %Y" }}</time></span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endif %}{% endfor %}
  </ul>
  {% endunless %}{% endfor %}
</div>