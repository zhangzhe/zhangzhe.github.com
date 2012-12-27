---
layout: post
title: "solve 'single table inheritance' problem with view"
description: ""
category: Rails
tags: [single_table_inheritance, view]
---
{% include JB/setup %}


I want to read data cross database, so I create a model page_group.rb and code like: 

{% highlight ruby %}
class PageGroup < ActiveRecord::Base
  ActiveRecord::Base.establish_connection(
    :adapter  => "mysql",
    :host     => "localhost",
    :username => "myuser",
    :password => "mypass",
    :database => "somedatabase"
  )
end
{% endhighlight %}



If PageGroup model in source app use single-table inheritance, then in console: <br>
`PageGroup.first`

will got error: 

----
    ActiveRecord::SubclassNotFound: The single-table inheritance mechanism 
    failed to locate the subclass: 'sss'. This error is raised because the
    column 'type' is reserved for storing the class in case of inheritance.
    Please rename this column if you didn't intend it to be used for 
    storing the inheritance class or overwrite PageGroup.inheritance_column 
    to use another column for that information.
    
    
Solution is create view for the table use single-table inheritance(PageGroup) in source app: 
{% highlight ruby %}
execute("CREATE VIEW extra_lessons AS SELECT id, title, blabla FROM page_groups")  
{% endhighlight %}


solved:) 