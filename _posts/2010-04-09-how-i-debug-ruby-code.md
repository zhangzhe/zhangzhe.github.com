---
layout: post
title: "How I debug ruby code"
description: ""
category: Rails
tags: [debug]
---
{% include JB/setup %}


Debugging code is so important. 
I would like to share my ways. Enjoy:) 


**1. put in anywhere of the code need to debug**<br>
`puts something`

it will output _something_ into logs. 
This is the basic way but very easy, further more you can use tap to make it easier. 

<br>
**2.later I wrote a method in lib/my_puts.rb like**<br>
{% highlight ruby %}
def gp(s = "")
  puts "\n\n<<<<<<<<<<<<<<<<<<<<<<<<<"
  puts s
  puts "<<<<<<<<<<<<<<<<<<<<<<<<<\n\n"
end
{% endhighlight %}


and put into enviroment.rb<br>
`require 'my_puts'`

So in Rails enviroment, use 
{% highlight ruby %}
pg "I am strong"
{% endhighlight %}


will output<br>
`<<<<<<<<<<<<<<<<<<<<<<<<<
I am strong
<<<<<<<<<<<<<<<<<<<<<<<<<`


It make the debug info more easy to be found. 
<br>

**3.Debugger is the final debug solution. It even can be use in writing test code or rake task.**<br>

Just puts debugger in your code. 
Most useful commands in ruby-debug 

* list
* irb
* p

Do not forget to delete the debugger afterward:) 

<br>
**4.Of course, if use TDD or BDD and meeting 100% code coverage, no need debug any more.**