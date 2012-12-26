---
layout: post
title: "add profiling and profiling tool"
description: ""
category: Rails
tags: [profiling]
---
{% include JB/setup %}


Just added Cache to my blog. The performance enhancements were great! 

New cache in rails is so good, just put code to be cached in a block is OK. 
{% highlight ruby %}
<% cache @blog do %>
  <ht>
    <%= link_to h(@blog.title), blog_path(@blog) %>
    <%= link_to "(#{@blog.brother.type} version)", blog_path(@blog.brother) %>
  </ht>
  <hd><%= @blog.created_at.to_date %></hd>
  <div>
    <%= g_textile @blog.content %>
  </div>
<% end %>  
{% endhighlight %}


About Profiling tool, New Relic is a god choise. <br>
Install newrelic as a plugin seems not work. so install the gem 

`gem install newrelic_rpm`

and add to environment 
{% highlight ruby %}
config.gem "newrelic_rpm"  
{% endhighlight %}


Below is the comparation of time spend on home page without cache and with cache for 10 times from newrelic. 


I cann't find the page ... But it got about 300% performance enhancement. Cool!