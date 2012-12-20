---
layout: post
title: "电脑升级记录 "
description: ""
category: Journal
tags: [Ubuntu, PC]
---
{% include JB/setup %}

去年十一买的R61e如今已经不堪重负，终于升级。 
之前已经升级了内存到3G(实际买的两根4G内存，无奈装的32位系统支持不好，换成了3G)。 
现在开发时切换程序经常需要较长的等待时间，有时系统甚至暂时没有反应。由于工作的娱乐都使用这一台电脑，原配的120G硬盘常常捉襟见肘了。 
因此想换一块好一点的处理器，并加一块大硬盘，升级下来的硬盘做移动硬盘继续使用。 

处理器看好t7300（淘宝）,性能不俗，而且完美支持小黑的GM965主板。硬盘看好日立的7K320，7200转（京东）。硬盘盒看好元谷星钻iPD-USB（京东）。 
提交订单，到货，安装。一切顺利：） 

但安装系统和软件过程出乎意料的麻烦，以为曾经装过的会很顺利，没想到竟耽误了近10个小时。 
下面是安装过程以及一些心得。 

**Ubuntu**<br>
因为着急用，手头又没有9.10的安装盘。就想安装了8.10之后升级上去。后来缓慢的升级过程证明这是一个非常笨的错误。 
发现Ubunut Tweak很好用阿：） 

**Ruby on Rails**<br>
或许因为是逐渐升级的缘故，安装过程中遇见很多困难，解决时使用一些命令和心得： 

- apt-cache search ruby
- dpkg -L ruby：检查装了什么
- dpkg -S：查依赖关系
- which ruby：调用ruby的命令地址
- ls -L /usr/bin/ruby：显示符号链接
- gem 装在/usr/lib/ruby/gems/1.8
- gem list 
- /etc/hosts：本机域名配置地址

**gem**<br>
gem的安装让我崩溃，最后怒把老硬盘里gems原封不动考了过来。 


**github和heroku**<br>
升级完之后发现github和heroku账户都无法commit了。 
解决方法如下<br>
1.生成key <br>
`ssh-keygen -d `

2.加入key <br>
heroku: 运行`heroku keys:add` , 输入邮箱和密码, heroku解决。 <br>
github: 拷贝/home/gary/.ssh/id_dsa.pub中内容到github账户"Deploy Keys"中, github解决。 


PS, 升级后系统性能的提升让我非常满意。 