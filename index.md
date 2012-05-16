---
layout: page
title: Keep simple & live Strong
tagline: Supporting tagline
---
{% include JB/setup %}

My name is Gary Zhang(zhangzhe). I live in BeiJing, China.

I am a programmer working for GLODON SOFTWARE COMPANY LIMITED.

I love beautiful codes and try my best writing them.

I am crazy about cycling, running and swimming. I took part in marathon three times, triathlons four times, one 70.3 ironman and lots of other games.

In this blog, I will share some technique things at work, also part of my personal life. Enjoy:)![Alt text](/images/portrait.jpg "me")
<br>

***

<br>

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span>&nbsp; &raquo; &nbsp; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>



