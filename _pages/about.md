---
permalink: /
title: "Welcome"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---


Welcome to my personal website. I mostly use it to write some informative posts, which cover anythign I can do in the lab. This goes from computer setup, network setup, simulation tricks and experimental setup. 



# Lastest tutorial 

{% for post in site.posts limit 2 %}
  <li><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
      {{ post.content | strip_html | truncatewords:150}}<br>
          <a href="{{ post.url }}">Read more...</a><br><br>
{% endfor %}