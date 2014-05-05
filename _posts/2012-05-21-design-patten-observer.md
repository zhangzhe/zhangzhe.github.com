---
layout: post
title: "设计模式之'观察者模式'"
categories: 设计模式学习
---


#模式描述

`观察者模式` 是一种行为型模式。 （行为型模式描述类或对象如何交互及如何分配职责，它主要涉及通过合理的处理方法，达到使系统升级性和维护性提高的目的。）

基本描述： 观察者模式定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象。这个主题对象在状态上发生变化时，会通知所有观察者对象，让他们能够自动更新自己。

详细描述：观察者模式有时又被称为发布-订阅（Publish/Subscribe）模式、模型-视图（Model/View）模式、源-收听者（Source/Listener）模式或从属者（Dependents）模式。 在这种模式中，一个目标对象管理所有相依于它的观察者对象，在它自身状态改变时主动通知其观察者对象。观察者模式通常被用来作事件处理系统。

#Observer模式的典型应用

- 侦听/监视某个对象的状态变化
- 发布者/订阅者(publisher/subscriber)模型中，当一个外部事件被触发时，通知邮件列表中的订阅者


***

<br>
#实例1 博客系统：

- 管理订阅者
- 可以被订阅
- 当有更新时，会自动通知订阅者
<br>

{% highlight ruby %}

class Article
  attr_accessor :title
  @subscribers = []

  def initialize(title)
    @title = title
    notify_subscribers
  end

  class << self
    attr_accessor :subscribers

    def add_subscriber(subscriber)
      @subscribers << subscriber
    end

    def remove_subscriber(subscriber)
      @subscribers.delete subscriber
    end
  end

  private
  def notify_subscribers
    Article.subscribers.each do |subscriber|
      subscriber.notify_new_article(self)
    end
  end
end


require 'article'

describe Article do
  before :all do
    User = Class.new
    class Reader
      def self.notify_new_article(article)
        p "Yeah, there is a new article has been writen,
				title with '#{article.title}'."
      end
    end

    class GFW
      def self.notify_new_article(article)
        p "I am here"
        p "a new article names '#{article.title}' has been writen. GFW knows."
      end
    end
  end

  describe "add_subscriber" do
    it "add subscriber to Article" do
      expect { Article.add_subscriber(User) }.to
			change(Article.subscribers, :count).by(1)
      Article.subscribers.last.should == User
    end
  end

 describe "remove_subscriber" do
   it "remove subscriber to Article" do
     expect { Article.remove_subscriber(User) }.to
		 change(Article.subscribers, :count).by(-1)
   end
 end

 describe "if a new blog is created, all the subscribers get the message" do
   it do
     Article.add_subscriber(Reader)
     Article.add_subscriber(GFW)
     GFW.should_receive(:notify_new_article).once
     Reader.should_receive(:notify_new_article).once
     article =  Article.new("I am Strong!!")
   end
 end
end

{% endhighlight %}



***

<br>

# 实例 2 微博：

- 用户间可以相互 follow
- 用户更新微博时通知其 followers

<br>

{% highlight ruby %}

class User
  attr_accessor :followers, :name

  def initialize(name)
    @name = name
    @followers = []
  end

  def create_a_weibo(title)
    p "#{name} created weibo titles #{title}"
    notify_followers(title)
  end

  def follow(someone)
    someone.add_follower(self)
  end

  def unfollow(someone)
    someone.remove_follower(self)
  end

  def add_follower(someone)
    @followers << someone
  end

  def remove_follower(someone)
    @followers.delete(someone)
  end

  def notify_followers(title)
    followers.each do |follower|
      follower.notify_weibo(title)
    end
  end

  def notify_weibo(title)
    p "#{name} konws #{title}"
  end
end

require 'weibo'

describe "a User" do
  before :each do
    @gary = User.new("gary")
    @andy = User.new("andy")
    @seven = User.new("seven")
    @andy.follow(@gary)
    @seven.follow(@gary)
  end

  it "should follow someone and unfollow someone" do
    @gary.followers.should == [@andy, @seven]
    @andy.unfollow(@gary)
    @gary.followers.should == [@seven]
  end

  it "should notify followers after create a weibo" do
    @seven.should_receive(:notify_weibo).once
    @andy.should_receive(:notify_weibo).once
    @gary.create_a_weibo("I am strong!")
  end
end

{% endhighlight %}


***

<br>

总结：观察者模式定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象。这个主题对象的状态发生改变时，会通知所有的观察者对象，使它们能够自己更新自己。