---
layout: page
title: Hi, I'm Russ.
---
{% include JB/setup %}

<div style="float:right;text-align:center">
<img src="assets/me.jpg" class="me well" />
<br>
<a href="http://twitter.com/#!/russjf" class="zocial icon twitter" style="margin:5px;">twitter</a>
<a href="http://github.com/rf" class="zocial icon github" style="margin:5px;">github</a>
</div>

I'm a software engineer from New Jersey. Currently I'm employed by Rutgers 
University, where I write the Rutgers Mobile app for iPhones, iPads, and 
Android devices. I'm also an undergraduate student at Rutgers studying 
computer science.

Resume: [pdf](assets/resume-new.pdf)

-------

<ul>{% for post in site.posts %}
   <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}</ul>
