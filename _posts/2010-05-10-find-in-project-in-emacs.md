---
layout: post
title: "'find in project' in emacs"
description: ""
category: Tool
tags: [emacs]
---
{% include JB/setup %}


Reference [Doug Alcorn\'s method](http://blog.lathi.net/articles/2007/11/07/navigating-your-projects-in-emacs "Doug Alcorn\'s method"). First install [exuberant ctags](http://ctags.sourceforge.net "exuberant ctags").

Create a rake task for creating tags for current project. 

{% highlight ruby %}
module Tags
  RUBY_FILES = FileList['**/*.rb']
end
namespace "tags" do
  task :emacs => Tags::RUBY_FILES do
    puts "Making Emacs TAGS file"
    sh "/usr/bin/ctags -e #{Tags::RUBY_FILES}", :verbose => false
  end
end
task :tags => ["tags:emacs"]
{% endhighlight %}


run task <br>
`rake tags:emacs`

<br>

Now can use `tags-search` to search tags; and use `tags-loop-continue` to loop tags, cool:) 

It will be more handy using shortcut. what I did: 

----
    (global-set-key (kbd "C-c C-c C-s") 'tags-search)
    (global-set-key (kbd "C-c C-c C-n") 'tags-loop-continue)

Right now it can search all the *.rb files, but not others. It works, but not perfect:( 

<br>

Two very handy shortcut 

----
    C-h w         where-is         
    C-h k         describe-key

<br>

Refreshed my emacs config and shortcut again. Most popular shortcuts: 

----
    C-c C-c C-c                rinari-find-controller
    C-c C-c C-m                rinari-find-model
    C-c C-c C-v                rinari-find-view
    C-c C-c C-r                rinari-find-rspec
    C-c C-c C-f                rinari-find-file-in-project
    C-c C-c C-h                hide-all-blocks
    C-c C-c C-t                toggle-hiding-block
    C-c C-c C-o                comment-region
    C-c C-c C-u                uncomment-region
    C-c C-c C-g                goto-line