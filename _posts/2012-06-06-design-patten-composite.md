---
layout: post
title: "设计模式之'组合模式'"
description: ""
category: DesignPattern
tags: [设计模式, Ruby]
---
 

#模式描述

`组合模式` 是一种结构型模式。

基本描述： 其意图是将对象组合成树形结构以表示部分-整体的层次结构。

详细描述：组合模式使得用户对单个对象和组合对象的使用具有一致性。
组合模式模式的一个重要思想是递归组合，关键是一个抽象类，它既可以代表组合对象，又可以代表一个被组合的对象。

关于组合模式的一个普遍性的例子是你每次使用电脑时所遇到的:文件系统。文件系统由目录和文件组成。每个目录都可以装内容。目录的内容可以是文件，也可以是目录。按照这种方式，计算机的文件系统就是以递归结构来组织的。


#组合模式的适用场景
一类具有“容器特征”的对象——即他们在充当对象的同时，又是其他对象的容器的情况。
想到组合模式就应该想到树形结构图。

<br>

***

<br>

# 实例1
#做晚餐包括准备蛋糕和酒。准备蛋糕和酒又分别包括：
- 准备蛋糕：准备奶油、准备干配料、准备液态配料、搅拌
- 准备酒：准备可乐、准备伏特加、搅拌

<br>
# 算法1
{% highlight ruby %}
class Task
  attr_reader :name, :sub_tasks
  
  def initialize(name)
    @name = name
    @sub_tasks = []
  end
  
  def add_sub_task(task)
    @sub_tasks << task
  end
  
  def time_required
    time = 0.0
    @sub_tasks.each { |t| time += t.time_required }
    return time
  end
  
  def list_sub_tasks
    @sub_tasks.each do |sub_task|
      p sub_task.name
      if sub_task.sub_tasks.size > 0
        sub_task.list_sub_tasks
      end
    end
  end
end

class AddDryIngredientsTask < Task
  def initialize
    super("Add Dry Ingredients")
  end
  
  def time_required
    1.0
  end
end

class AddLiquidsTask < Task
  def initialize
    super("Add Liquids")
  end
  
  def time_required
    1.0
  end
end

class MakeBatterTask < Task
  def initialize
    super("Make Batter")
  end
  
  def time_required
    1.0
  end
end

class MixTask < Task
  def initialize
    super("Mix up")
  end
  
  def time_required
    3.0
  end
end

class MakeBatterCakeTask < Task
  def initialize
    super("Make BatterCake")
    add_sub_task(MakeBatterTask.new)
    add_sub_task(AddDryIngredientsTask.new)
    add_sub_task(AddLiquidsTask.new)
    add_sub_task(MixTask.new)
  end
end

class AddCocaTask < Task
  def initialize
    super("Add Coca")
  end
  
  def time_required
    2.0
  end
end

class AddVodkaTask < Task
  def initialize
    super("Add Vodka")
  end
  
  def time_required
    4.0
  end
end

class MakeWineTask < Task
  def initialize
    super("Make Wine")
    add_sub_task(AddCocaTask.new)
    add_sub_task(AddVodkaTask.new)
    add_sub_task(MixTask.new)
  end
end

class MakeDinnerTask < Task
  def initialize
    super("Make Dinner")
    add_sub_task(MakeBatterCakeTask.new)
    add_sub_task(MakeWineTask.new)
  end
end

describe "Task" do
  it "should work" do    
    task = MakeDinnerTask.new
    task.time_required.should == 15
    task.list_sub_tasks
  end
end


{% endhighlight %}

<br>

# 算法2

{% highlight ruby %}
class Task
  attr_accessor :sub_tasks
  attr_reader :require_time, :name
  
  def initialize(name= "Task", self_require_time=0)
    @self_require_time = self_require_time
    @name = name
    @sub_tasks = []
  end
  
  def add_sub_task(sub_task)
    @sub_tasks << sub_task
  end
  
  def require_time
    @sub_tasks.map(&:require_time).inject(@self_require_time){|i, j| i+j}
  end
  
  def list_sub_tasks
    @sub_tasks.each do |sub_task|
      p sub_task.name
      if sub_task.sub_tasks.size > 0
        sub_task.list_sub_tasks
      end
    end
  end
end

describe "Task" do
  before :all do
    @make_dinner = Task.new("Make Dinner")
    make_wine = Task.new("Make Wine")
    make_batter_cake = Task.new("Make BatterCake")

    make_batter = Task.new("Make Batter", 1)
    add_dry_ingredients = Task.new("Add Dry Ingredients", 1)
    add_liquids = Task.new("Add Liquids", 1)

    add_coca = Task.new("Add Coca", 2)
    add_vodka = Task.new("Add Vodka", 4)

    mix_up = Task.new("Mix Up", 3)

    @make_dinner.add_sub_task(make_wine)
    @make_dinner.add_sub_task(make_batter_cake)

    make_wine.add_sub_task(add_coca)
    make_wine.add_sub_task(add_vodka)
    make_wine.add_sub_task(mix_up)

    make_batter_cake.add_sub_task(make_batter)
    make_batter_cake.add_sub_task(add_dry_ingredients)
    make_batter_cake.add_sub_task(add_liquids)
    make_batter_cake.add_sub_task(mix_up)
    
  end
  
  it "should work" do    
    @make_dinner.list_sub_tasks
    @make_dinner.require_time.should == 15
  end
end

{% endhighlight %}

<br>

***

<br>
# 实例2

# 部门和子部门的预算管理：部门有自己的预算，子部门也有自己的预算；部门可以包括子部门，子部门也可以包含子部门；计算部门的整体预算。

{% highlight ruby %}
class Department
 def initialize(self_budget)
   @sub_departments = []
   @self_budget = self_budget
 end
 
 def add_sub_department(sub_department)
   @sub_departments << sub_department
 end
 
 def remove_sub_department(sub_department)
   @sub_departments.delete sub_department
 end
   
 def budget
   @sub_departments.map(&:budget).inject(@self_budget)
	 {|self_budget, budget| self_budget+budget }
 end
end

describe "Department" do
  before :all do
    @grandsoft = Department.new(5000)
    cmd = Department.new(300)
    ccd = Department.new(500)
    ced = Department.new(500)
    @grandsoft.add_sub_department(cmd)
    cmd.add_sub_department(ccd)
    cmd.add_sub_department(ced)
  end
  
  it "should work" do    
    @grandsoft.budget.should == 6300
  end
end


{% endhighlight %}

<br>

***

<br>

# 总结
组合模式解耦了客户程序与复杂元素内部结构，从而使客户程序可以向处理简单元素一样来处理复杂元素。
如果你想要创建层次结构，并可以在其中以相同的方式对待所有元素，那么组合模式就是最理想的选择。

