---
layout: post
title: "solve autotest always rerun problem"
description: ""
category: Rails
tags: [test autotest]
---
{% include JB/setup %}


Run autospec，found even no change and save happens, autotest will rerun time and time... 


After research I found it happens because of a gem (idp_gem): after test, it will update file spec/spec\_helper.rb, and this file will trigger next time's autotest. So endless loop 


The solution is easy，add to .autotest 
Autotest.add_hook :initialize do |at|
  %w{spec/spec_helper.rb}.each {|exception| at.add_exception(exception)}
end

Problem solved:) 

<hr>
PS,<br>
`find ./ -cmin -1`<br>
This command will show all files that changed in last minute. 
