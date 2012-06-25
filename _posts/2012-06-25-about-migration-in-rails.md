---
layout: post
title: "about migration in Rails"
description: ""
category: 
tags: []
---
{% include JB/setup %}


##0. 一个发现：
{% highlight ruby %}
class TestMigrationLock < ActiveRecord::Migration
  def up
    remove_column :articles, :content
    sleep 1000
  end

  def down
  end
end
{% endhighlight %}

运行Rails应用的同时运行上述的migration会出现什么情况？

##答案是:
- 使用 postgresql 数据库，应用暂时无法访问了。
- 使用mysql或sqlit3数据库，应用可以正常访问。


<br>
##结论：
`运行对表结构改变的migration时，postgresql会锁定对应表，mysql和sqlit3不会锁。`

<br><br>
***

<br><br>
##下面整理写Migration时容易忽略的地方和需要注意的事项。

##1. **up and down**: migration目的是做数据迁移，所以一定要可进可退。但有一些删除或修改的操作没法回退，则一定要有明确说明，并且在down中 raise ActiveRecord::IrreversibleMigration，下面第五点中会有详细说明。

<br>
##2. rails 3.1新增加了change方法，用来处理增量类操作，如增加表或增加属性，Migration会自动为我们加入回退。例：
{% highlight ruby %}
class CreateProducts < ActiveRecord::Migration
  def change
    create_table: products do|t|
      t.string: name
      t.text: description
    end
  end
end
{% endhighlight %}

<br>

##3. Migration是类，它继承自 ActiveRecord::Migration。所以可以直接调用它，可以为它增加方法。当然和业务逻辑相关的方法还是建议写在对应model中。

<br>

##4. Migration中对应的操作会被包在`事务`中。

<br>

##5. 不可更改的操作。如果有些操作确实不可更改，比如删除操作，那一定要有明确说明，并且在down中raise ActiveRecord::IrreversibleMigration。例：

{% highlight ruby %}
class RemoveEmptyTags<ActiveRecord::Migration
  def up
    Tag.all.each { |tag|tag.destroy if tag.pages.empty? }
  end

  def down
    raise ActiveRecord::IrreversibleMigration, "Can't recover the deleted tags"
  end
end
{% endhighlight %}

<br>

##6. 需要直接写SQL的要使用方法 execute。例：
{% highlight ruby %}
classMakeJoinUnique<ActiveRecord::Migration
  def up
    execute "ALTER TABLE `pages_linked_pages` ADD UNIQUE `page_id_linked_page_id` (`page_id`,`linked_page_id`)"
  end

  def down
    execute "ALTER TABLE `pages_linked_pages` DROP INDEX `page_id_linked_page_id`"
  end
end
{% endhighlight %}

<br>

##7. 在修改某个model之后再立刻使用它时，有时当前model的表还没有更新到最新会造成错误，可以通过重置解决这个问题。例：
{% highlight ruby %}
class AddPeopleSalary<ActiveRecord::Migration
  def up
    add_column:people, :salary, :integer
    Person.reset_column_information
    Person.all.each do |p|
      p.update_attribute:salary, SalaryCalculator.compute(p)
    end
  end
end
{% endhighlight %}

<br>

##8. 方法 `say & say_with_time`例：
{% highlight ruby %}
class CreateProducts < ActiveRecord::Migration
  def up
    create_table :products do |t|
        t.string :name
        t.text :description
        t.timestamps
    end
    10.times do
      p = Product.create
      say "create product #{p.id}"
    end
  end
end
{% endhighlight %}

<br>

##9.使用 bulk 参数加速改表操作。[http://m.onkey.org/bulk-alter-table-with-rails-3-and-mysql](http://m.onkey.org/bulk-alter-table-with-rails-3-and-mysql)

<br>

##10.使用add_index增加索引时如如遇默认索引名过长的情况使用name指定索引名。

<br>

##11.在重建新的项目时使用 `rake db:schema:load` 而不是从头运行 `rake db:migrate`

<br>

##12. migration常用命令：
{% highlight ruby %}
   rake db:create
   rake db:create:all
   rake db:drop
   rake db:drop:all
   rake db:migrate
   rake db:rollback STEP=n
   rake db:migrate:up VERSION=20080906120000
   rake db:migrate:down VERSION=20080906120000
{% endhighlight %}

<br>

##13. Rails中migration的实现是典型的`命令模式`。

<br>

***

更多相关介绍请参考：
<br>
http://api.rubyonrails.org/classes/ActiveRecord/Migration.html
<br>
http://guides.rubyonrails.org/migrations.html