---
layout: post
title: include 和 extend 的区别
categories: "学习&实践&思考"
---

请看下面的代码：

{% highlight ruby %}

module Gary
  def hello
    puts "hello"
  end
  def self.world
    puts "world"
  end
end
class A
  include Gary
end
class B
  extend Gary
end
A.new.hello # => "hello"
B.hello #     => "hello"
A.world # throw error: undefined method `world' for A:Class (NoMethodError)
B.world # throw error: undefined method `world' for B:Class (NoMethodError)

{% endhighlight %}


结论:

* include 让目标类可以以实例方法的形式调用包含的module中的实例方法;
* extend 让目标类可以以类方法的形式调用包含的module中的实例方法;
* include 和 extend 都无法调用包含的module中的类方法。