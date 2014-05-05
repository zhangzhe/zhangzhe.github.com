---
layout: post
title: "Textile WYSIWYG editor"
categories: 开发及辅助工具
---

Working on Textile & WYSIWYG editor these days.
What I need is a WYSIWYG editor for editing content with styles like bold, color, list, etc.. And I do not want to save HTML directly into database, so Textile seems a good choose.

Check [here](https://github.com/zhangzhe/g_editor "here") for more details.

I would like to share some javascript regular expression code.
After writing these code, I felt it is so lucky to working with Ruby.

{% highlight javascript %}
textilize: function(html, escape){
        html = html.replace(/<br ?\/?>/gi, "\n");
        html = html.replace(/<(?:b|strong)>((.|[\r\n])*?)<\/(?:b|strong)>/gi, '*$1*');
        html = html.replace(/<(?:i|em)>((.|[\r\n])*?)<\/(?:i|em)>/gi, '_$1_');
        html = html.replace(/<(?:u|ins)>((.|[\r\n])*?)<\/(?:u|ins)>/gi, '+$1+');
        html = html.replace(/<a href="(.*?)">((.|[\r\n])*?)<\/a>/gi, '"$2":$1');
        html = html.replace(/<font color="#0000ff".*?>((.|[\r\n])*?)<\/font>/gi,
          '%{color:blue}$1%');
        html = html.replace(/<font color="#FF0000".*?>((.|[\r\n])*?)<\/font>/gi,
          '%{color:red}$1%');
        html = html.replace(/<font color="#088A4B".*?>((.|[\r\n])*?)<\/font>/gi,
          '%{color:green}$1%');
        //list
        var r = /([\s\S]*)<ul>([\s\S]*?)<\/ul>([\s\S]*)/;
        while (r.test(html))
            {
                p = RegExp.$1;
                c = RegExp.$2;
                e = RegExp.$3;
                c = c.gsub(/<li>([\s\S]*?)</li>/, "* #{1}\n");
                html = p + "\n" + c + e;
            }
        html = html.replace(/^[\r\n]+|[\r\n]+$/g, '');
        return html;
    },

htmlize: function(textile, escape){
        //escape
        textile = textile.replace(/\/gi, "0142540974231612");
        textile = textile.replace(/\\_/gi, "0750023893692338");
        textile = textile.replace(/\\*/gi, "0643004879835027");
        textile = textile.replace(/\n/gi, '<br/>');
        textile = textile.replace(/\*(.+?)\*/gi, (this.internetExplorer ?
        '<strong>$1</strong>' : '<b>$1</b>'));
        textile = textile.replace(/_(.+?)_/gi, (this.internetExplorer ?
        '<em>$1</em>' : '<i>$1</i>'));
        textile = textile.replace(/(.+?)/gi, '<u>$1</u>');
        textile = textile.replace(/"(.+?)":([^\s\n<]+)/gi, '<a href="$2">$1</a>');
        textile = textile.replace(/%{color:blue}(.+?)%/gi,
        '<font color="#0000ff">$1<\/font>');
        textile = textile.replace(/%{color:red}(.+?)%/gi,
        '<font color="#FF0000">$1<\/font>');
        textile = textile.replace(/%{color:green}(.+?)%/gi,
        '<font color="#088A4B">$1<\/font>');
        textile = textile+'<br\/>';
        textile = textile.replace(/<p>[\s\n]*<\/p>/g, '');
        //unescape
        textile = textile.replace(/0142540974231612/, "\");
        textile = textile.replace(/0750023893692338/, "\\_");
        textile = textile.replace(/0643004879835027/, "\\*");
        //list
        var r = /(<br\/>\* .*?<br(\/)*>)(?!\* )/;
        while (r.test("<br/>" + textile))
            {
                l = RegExp.leftContext;
                m = RegExp.lastMatch;
                e = RegExp.rightContext;
                m = m.gsub(/\* (.*?)<br(\/)*>/, "<li>#{1}</li>").gsub("<br/>", "");
                textile = l + "<ul>" + m + "</ul>" + e;
            }
        return textile;
    },
{% endhighlight %}


**Importent**:
"JavaScript and VBScript do not have an option to make the dot match line break characters. In those languages, you can use a character class such as [\s\S] to match any character."
see [here](http://www.regular-expressions.info/dlot.htm "here")  for more details.


So when in Ruby we use:

{% highlight ruby %}
/(.*)<ul>(.*?)<\/ul>(.*)/
{% endhighlight %}


In Javascript we need code like:

{% highlight javascript %}
/([\s\S]*)<ul>([\s\S]*?)<\/ul>([\s\S]*)/
{% endhighlight %}

What a pain ...

<hr>

Afterward<br>
later on, I found a more light weight textile editor from Redmine. After simple modification, I got [this](https://github.com/zhangzhe/geditor "this") . I would like to recommend this one unless editing very complex format.
