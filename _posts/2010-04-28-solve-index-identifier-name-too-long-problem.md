---
layout: post
title: "solve index 'Identifier name too long' problem"
description: ""
category: Rails
tags: [index]
---
{% include JB/setup %}


It is easy to add index in rails <br>
in migration: 
{% highlight ruby %}
add_index(:test_statistics, [:page_id, :study_record_id, :student_id])
{% endhighlight %}


But sometimes mysql can not handle it because of "Identifier name too long" error:<br>

----
    Mysql::Error: Identifier name 
    'index_test_statistics_on_page_id_and_study_record_id_and_student_id' 
    is too long: CREATE  INDEX 
    index_test_statistics_on_page_id_and_study_record_id_and_student_id` 
    ON `test_statistics` (`page_id`, `study_record_id`, `student_id`)

solution is go into dbconsole, manual create index using shorter identifier name: <br>

----
    CREATE  INDEX `my_pss_index` ON `test_statistics` (`page_id`, `study_record_id`, `student_id`)

or with option:
{% highlight ruby %}
add_index(:test_statistics, [:page_id, :study_record_id, :student_id], :name =>"my_pss_index")
{% endhighlight %}

solve：） 