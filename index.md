---
layout: page
title: Keep simple & live Strong
tagline: Supporting tagline
title: Keep simple & live Strong
---
{% include JB/setup %}

Hi there! My name is Gary Zhang(zhangzhe), I live in BeiJing, China.

I am a programmer working for GLODON SOFTWARE COMPANY LIMITED.

I love beautiful code and try my best writing them.

I am crazy about cycling, running and swimming. I took part in marathon four times, triathlons four times, 70.3 ironman one time and lots of other games.

In this blog I will share some technical stuff at work, also part of my personal life. Enjoy:)  <br>
![Alt text](/images/portrait.jpg "me")


<br>

***

<br>


<ul>
  {% for post in site.posts %}

    {% unless post.next %}
      <h3>{{ post.date | date: '%Y' }}</h3>
    {% else %}
      {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
      {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
      {% if year != nyear %}
        <br>
        <h2>{{ post.date | date: '%Y' }}</h2>
      {% endif %}
    {% endunless %}
    
    <li>{{ post.date | date: "%b %d" }} <a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
