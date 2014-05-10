---
layout: post
title: "关于使用脱机版的Rails API 和 guides"
categories: "学习&实践&思考"
---

[http://api.rubyonrails.org/](http://api.rubyonrails.org/ "http://api.rubyonrails.org/") & [http://guides.rubyonrails.org/](http://guides.rubyonrails.org/ "http://guides.rubyonrails.org/")


使用`Ruby On Rails`做开发对这两个网站一定不会陌生。相信不管是新手还是老鸟都能从上边找到自己需要的信息。

当然我们可以通过访问上述网址在线查看，但是有时网速问题或无法上网还是最造成一些不变。那如果可以离线使用就太好了。

下面介绍如何把这两个东西搞到你的本机上。


##1. 离线 Rails Guides
首先新建一个rails应用，不妨就叫railsguides

`rails new railsguides`

接下来加入 RedCloth 到 Gemfile

`gem 'RedCloth'`

运行 bundle 完成安装

接下来运行下面的命令

`rake doc:guides`

现在去doc目录下找你的guides吧。
<br>
<br>
##2. 离线 Rails API
Rails为我们提供了生成doc的方法，但是我更喜欢这个方便查找的版本。
直接去[http://railsapi.com/](http://railsapi.com/ "http://railsapi.com/")下载吧，同时附赠了Ruby API，还可以定制。
<br>
<br>
##结论
我个人喜欢把API和guides放到项目目录下，然后在浏览器为index建立书签，方便在开发同时查阅。(当然你更可以把他们搞到移动端，如`ipad`或`iphone`上。个人感觉使用`goodreader`查看guide效果很好，查API有些不方便。)