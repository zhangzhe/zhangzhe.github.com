---
layout: post
title: "'find in project' for emacs"
categories: "学习&实践&思考"
---


Reference [Doug Alcorn's method](http://blog.lathi.net/articles/2007/11/07/navigating-your-projects-in-emacs "Doug Alcorn\'s method").

#### 1. First install [exuberant ctags](http://ctags.sourceforge.net "exuberant ctags").


#### 2. Then create rake task for creating tags.

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



And run task
  `rake tags:emacs`



#### 3. Now can use `tags-search` to search tags, and `tags-loop-continue` to loop the tags, cool:)

It will be more handy using shortcut. So I config it as:


	(global-set-key (kbd "C-c C-c C-s") 'tags-search)
	(global-set-key (kbd "C-c C-c C-n") 'tags-loop-continue)

It works with all the *.rb files.

Two other very handy shortcut

	C-h w         where-is
	C-h k         describe-key


<br>
<hr>
<br>

Btw, refreshed my emacs config and shortcut, most popular shortcuts:

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