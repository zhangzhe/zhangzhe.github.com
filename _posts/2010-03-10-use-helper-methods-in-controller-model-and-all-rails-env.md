---
layout: post
title: "use helper methods in controller, model, and all Rails env"
description: ""
category: Rails
tags: [helper tip]
---
{% include JB/setup %}


I wanted to use helper methods in controller, model, or even rake. 

So I add 
{% highlight ruby %}
require 'helpers/application_helper'
include ApplicationHelper
{% endhighlight %}

Then I can use methods of ApplicationHelper. But if these methods call other helper methods from out side, I got "undefined method" error. 
    
Wired thing is，the "include" way works well in gem "Idp::File" when include helper. So I try the way gem include module and add in controller: 
{% highlight ruby %}
ActionView::Base.send(:include, Student::CourseRecordsHelper)
{% endhighlight %}

The "undefined method" did not show up, but got 

----
    The error occurred while evaluating nil.url_for
    /usr/lib/ruby/gems/1.8/gems/actionpack-2.3.4/lib/action_view/helpers/url_helper.rb:85:in `send'
    /usr/lib/ruby/gems/1.8/gems/actionpack-2.3.4/lib/action_view/helpers/url_helper.rb:85:in `url_for

I also tried include all need modules, but still got the same error. 

<br>
<br>

Trying to find other way, and got two: 

1. ActionController::Base.helpers.link_to("", "")
2. @template.link_to("", "")


The first way can be used in controller, model or any where in rails; the second way can only be used in controller. [more details ](http://railscasts.com/episodes/132-helpers-outside-views "more details ").


But neither of them can solve my problem. First, methods I want to call are defined by myself，first way not work; second, where I want to call helper methods is in rake，second way not work either. 


Stop here, I did not find a way solving it. 

==== 

JP and seven advise me other way to achieve my requirement. Which make it easier and cleaner. 
I think next time meet this kind of problems, I should jump out and rethink. Some function difficult to achieve may because it is not reasonable. Just try other idea. 

====

Use helper can call helper methods in console 
`helper.truncate "Big sentence", 5`


<hr>
Afterward<br>
Helper methods are methods defined in module, just include what you need!!

