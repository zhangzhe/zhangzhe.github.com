---
layout: post
title: "设计模式之'策略模式'"
description: ""
category: design_patten
tags: [设计模式, ruby]
---
{% include JB/setup %}

#模式描述

`策略模式` 属于设计模式中对象行为型模式。

基本描述：策略模式是一个提倡“针对接口编程”的模式。定义好接口，接下来针对接口实现各类算法（策略）。

详细描述：在软件构建过程中，某些对象使用的算法可能多种多样，经常发生变化。如果在对象内部实现这些算法，将会使对象变得异常复杂，甚至会造成性能上的负担。
策略模式是告诉上下文：我需要去做什么，至于使用怎么实现，需要上下文去使用当前对应的策略去实现。策略模式和模板方法模式很相似，它们都是定义了一个算法，都可以相互替换而不影响客户的使用；不同的是模板方法模式使用的是继承来实现不同的算法，而策略模式是使用组合的方式来实现算法的替换；模板方法模式是定义了一个结构，而策略模式只是定义了一个接口。

举例来说：我想去上学。然后说我想坐自行车上学，就有人骑自行车带我去，具体怎么走我不知道，封装在那个人身上；我又说要坐汽车上学，就有人开车送我去，具体怎么开我不知道，封装在那个人身上；
所以说我只要 '说' 就好了，具体去法被封装起来了。
再比如说付款。我们可以定义几个付款类，然后根据用户的选择来决定用哪个付款类来付款，以达到在运行时切换付款方式，而不改变其他步骤的实现。

***

<br>
#实例1：付款有三种不同的付款方式：支票、信用卡、现金，但接口一致。模拟付款过程。

{% highlight ruby %}

class Customer
  def initialize(name, pay_way)
    @name = name
    @pay_way = pay_way
  end
  
  def name
    @name
  end
  
  def pay(number)
    @pay_way.pay(self, number)
  end
end

class Check
  def pay(customer, number)
    "#{customer.name} writes a check of #{number}!"
  end
end

class Cash
  def pay(customer, number)
    "#{customer.name} pays #{number} cash!"
  end
end

class CreditCard
  def pay(customer, number)
    "#{customer.name} pays #{number} with Credit Card!"
  end
end

describe "Customer" do
  it "should pay with check" do
    Customer.new("gary", Check.new).pay(500).should == 
"gary writes a check of 500!"
  end
  
  it "should pay with cash" do
    Customer.new("gary", Cash.new).pay(500).should == 
"gary pays 500 cash!"
  end
  
  it "should pay with credit card" do
    Customer.new("gary", CreditCard.new).pay(500).should == 
"gary pays 500 with Credit Card!"
  end
end

{% endhighlight %}


<br>

***

<br>

#实例2：创建 iphone、 ipad、 ipod 或者类似产品。具体创建过程不同，但创建接口一致。

{% highlight ruby %}

class Iphone
  def generate
    p "generating iphone"
  end
end

class Ipad
  def generate
    p "generating ipad"
  end
end

class ElectronicProduct
  def initialize(product)
    @product = product.new
  end
  
  def generate
    @product.generate
  end
end

ElectronicProduct.new(Iphone).generate
ElectronicProduct.new(Ipad).generate

{% endhighlight %}


<br>

***

总结：策略模式和模板方法模式很相似，它们都是定义了一个算法，都可以相互替换而不影响客户的使用；不同的是模板方法模式使用的是继承来实现不同的算法，而策略模式是使用组合的方式来实现算法的替换；模板方法模式是定义了一个结构，而策略模式只是定义了一个接口。