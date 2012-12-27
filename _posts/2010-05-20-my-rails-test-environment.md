---
layout: post
title: "my rails test environment"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Rspec and TDD are great. 
Would like to share my configuration and some experience, enjoy:) 


**1.generate test doc**<br>

use params `--format specdoc` will output test doc in term 
{% highlight ruby %}
spec spec/models/blog_spec.rb --format specdoc
{% endhighlight %}


Also can output test doc for all tests using commend<br>
`rake spec:doc`

But my favourite way is use `--format html` to output doc into a html file<br>
`spec spec/models/blog_spec.rb --format html:test.html`

 

When code coverage and organization are good enough, this doc even can use as documentation for code. 

A more convenient way is automate generate this doc after test or rcov, detail see 4 bellow. 

**2.generate code coverage**<br>

----
    rcov 
    rake spec:rcov

will generate html format [green]code coverage[/green]. 

 

`spec:rcov` by default will generate code coverage for all code under app/ and /lib, even there is no test file for them. It is not good in some condition, we can config `spec/rcov.opts` to change this. 
Just generate code coverage for models: <br>
`--exclude "spec/*,gems/*,app/controllers/*,app/helpers/*,app/sweepers/*,app/workers/*,lib/*"`

<br>

Code coverage is great way for checking the quality of code, but 100% coverage does not mean your code is perfect. As says in The Rspec Book: 
[green]So while low code coverage is a clear indicator that your specs need some work, high coverage does not necessarily indicate that everything is honky-dory.[/green] 

**3.rspec options**<br>

most useful options for me 

----
    --colour                               Show coloured (red/green) output
    --format o                           Specifies format for output: Profiling of 10 slowest examples
    --format html:test.html        Specifies format for output: A nice HTML report
    --diff                                   Show diff of objects that are expected to be equal when they are not
    --help                                  Help


All options are asstable, for example<br>
`spec spec/models/formatable_string_spec.rb --format specdoc --color`


**4.opts**<br>

Do not need to add options every time when test, just do configuration to spec/spec.opts

----
    --colour                     
    --format html:test.html  
    --diff


Combine the way of "generating test doc" in  1, add `--format html:test.html` to `spec/spec.opts` , will automate generate test doc after each test or rcov , the doc will output into test.html. 

**5.Autotest**<br>

Autotest is very cleaver, every time you save a test file, autotest will run that test file; and every time you save a library file, autotest will run the corresponding test file. If it sees that the previous failures are now passing, it loads up the entire suite and runs all of the examples again. 
When tried autotest on company project, there was a problem. Solution see solve autotest always rerun problem. 

<hr>

Test is good, but as says in Code Complete: <br>
Even considering the numerous kinds of testing available, testing is only one part of a good software-quality program. High-quality development methods, including minimizing defects in requirements and design, are at least as important.

**Do not excessive relay on test.**