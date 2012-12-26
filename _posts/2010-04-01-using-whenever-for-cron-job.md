---
layout: post
title: "using whenever for cron job"
description: ""
category: Rails
tags: [whenever, cron]
---
{% include JB/setup %}

Doing statistic things, need to statistic data from several databases and tables. 

So slow and optimization seems not effective. 

I want to make it not real time statistic: do cron job to count data at assign time and save the statistic result into database. 

It will be very easy to do this using whenever. 

**install**<br>
`gem install whenever`

write into config/environment.rb <br>
{% highlight ruby %}
Rails::Initializer.run do |config|
  config.gem 'whenever', :lib => false, :source => 'http://gemcutter.org/'
end  
{% endhighlight %}


go to root directory of app and generate cron file:<br>
`wheneverize .`

will generate schedule.rb at config/ , and in schedule.rb<br>
{% highlight ruby %}
set :output, "/home/app/applications/scenario/current/log/whenever.log"
every 1.minutes do
  runner "puts ('still working')"
  command "date"
end

every 1.day, :at => '02:00 am' do
  rake "update_course_record"
end
{% endhighlight %}

<br>

**importent** 
 1. set :output will output log into assigned log file, but if the file not exists, there is not error message. 
 2. command "date" will print current time; runner "puts Time.now" will print time the first time when whenever task converts to system task. 
 3. rake "update_course_record" is the cron task I want to run, it will statistic and save the result into database. I want it run at 2:00 am very day(when system is idle). 


Generate system task into /var/spool/cron/crontabs 
{% highlight ruby %}
$ whenever --update-crontab scenario
$ crontab -l
{% endhighlight %}


done:) 

If you need more logs about what happened, see [here](http://earthcode.com/blog/2009/05/rails/_script/_runner/_logging/_cron.html "here").
