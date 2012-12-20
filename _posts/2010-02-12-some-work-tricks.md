---
layout: post
title: "some tricks from work"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Read "The productive programmer" before, though could not understand a lot, I thought it was very useful to me. 
Today read "追求神乎其技的程式設計之道（十）":程式設計師的生產力之謎. Identify with author's opinin about productivity. 

Record some tricks from work, hope helps:) 

**1.create ubuntu shorter command**<br>
add to `~/.bashrc `<br>

----
    alias s='script/server'
    alias c='script/console'
    alias gz='cd /home/gary/idapted/zhangzhe/'

restart terminal. 

Now input `gz` in term goes to /home/gary/idapted/zhangzhe/ folder， 
under rails app root path，input `s` opens server, `c` open console. 


**2.Use shortcut**<br>
`Ubuntu`<br>
Use Compiz Config Settings Manager config with habit. 

----
    Alt + f       open Firefox
    Alt + e      open Emacs
    Alt + p      open Pidgin
    Alt + g      open Geditor


`Firefox`

----
    ctrl + shift + t      open last closed tab
    ctrl + k              into google search 
    ctrl + w              close current tab
    ctrl + t              open a new tab
    ctrl + l              into address field
    ctrl + tab            loop go to next tab
    ctrl + shift + tab    loop go to pre tab
    ctrl + f              search


**3.Use script to shorter command**<br>
For example, I use git do version control，deploy app to heroku. So after every iteration, I need to input into term: 

----
    git add .
    git commit . -m "some info"
    git push heroku master

to commit and deploy. 

All the command above can be simple by ruby script. 

I create a file `cmh` in app root, with ruby script 

----
    #!/usr/bin/env ruby
    def exe_cmd(cmd)
      puts cmd
      system cmd
    end

    cmd = "git add ."
    exe_cmd cmd

    args = ARGV.join(' ')
    cmd = "git commit . -m '#{ARGV[0]}'"
    exe_cmd cmd

    cmd = "git push heroku master"
    exe_cmd cmd


so I can call ./cmh "some info" in term for short to deploy. 

Another example, create a file calls `bf` in app root, with ruby script 

----
    #!/usr/bin/env ruby
    def exe_cmd(cmd)
      puts cmd
      system cmd
    end

    cmd = "heroku db:pull mysql://user@localhost/zhangzhe_bf?encoding=utf8"
    exe_cmd cmd

so I can call ./bf in term for short to backup. 

**4.improve ruby console**<br>
Wirble, a gem that improve your irb console with syntax coloring of output and input history. 
install <br>
`gem install wirble`

and put the following into `~/.irbrc`

----
    require 'rubygems'
    require 'wirble'
    Wirble.init
    Wirble.colorize


check [here](http://pablotron.org/software/wirble/ "here") for more details. 


**5.improve ruby console plus**<br>
When start learning Ruby, I kept thinking about, is there a way can edit code as easy as in a code_editor and check code output as easy as in `irb`. 
Today, found it:) 
`sketches` 

install:<br>
`gem install sketches`

Then require sketches in your `.irbrc` file:<br>
`require 'sketches'`

Sketches can be configured to use a custom editor command: <br>
`Sketches.config :editor => 'gedit'`


use in `irb`:<br>
`sketch`