---
layout: post
title: Incoding in ruby
categoriess: "学习&实践&思考"
---

Logging application log using ruby (1.9.3). Part of the data comes from Sina Weibo, and the log saves into file like："\xE7\xA8\x8B\xE5\xBA\x8F\xE5\x91\x98, \xE8\x8B\xB9\xE6\x9E\x9C\xE4\xBA\xA7\xE5\x93\x81\xE9\x87\x8D\xE5\xBA\xA6\xE4\xBE\x9D\xE8\xB5\x96\xE8\x80\x85, \xE7\x88\xB1\xE6\xB8\xB8\xE6\x88\x8F\xEF\xBC\x8C\xE7\x88\xB1\xE8\xBF\x90\xE5\x8A\xA8, \xE4\xBA\xAB\xE5\x8F\x97\xE7\x94\x9F\xE6\xB4\xBB”。



Here are some intresting thing found when solving the problem:


### 1. Abandoned $KCODE. $KCODE is abandoned in ruby 1.9.3:
`variable $KCODE is no longer effective; ignored`


### 2. The way JSON parse data. Data I want to parse looks like:
"{\"id\":2141219585,\"class\":1,\"name\":\"zzhang_\xE5\xBC\xA0\xE5\x93\xB2\",\"description\":\"\xE7\xA8\x8B\xE5\xBA\x8F\xE5\x91\x98, \xE8\x8B\xB9\xE6\x9E\x9C\xE4\xBA\xA7\xE5\x93\x81\xE9\x87\x8D\xE5\xBA\xA6\xE4\xBE\x9D\xE8\xB5\x96\xE8\x80\x85, \xE7\x88\xB1\xE6\xB8\xB8\xE6\x88\x8F\xEF\xBC\x8C\xE7\x88\xB1\xE8\xBF\x90\xE5\x8A\xA8, \xE4\xBA\xAB\xE5\x8F\x97\xE7\x94\x9F\xE6\xB4\xBB\"}"
I found out that With JSON parse I can got the data with right encoding:

require 'json'
data =  "{\"id\":2141219585,\"class\":1,\"name\":\"zzhang_\xE5\xBC\xA0\xE5\x93\xB2\",\"description\":\"\xE7\xA8\x8B\xE5\xBA\x8F\xE5\x91\x98, \xE8\x8B\xB9\xE6\x9E\x9C\xE4\xBA\xA7\xE5\x93\x81\xE9\x87\x8D\xE5\xBA\xA6\xE4\xBE\x9D\xE8\xB5\x96\xE8\x80\x85, \xE7\x88\xB1\xE6\xB8\xB8\xE6\x88\x8F\xEF\xBC\x8C\xE7\x88\xB1\xE8\xBF\x90\xE5\x8A\xA8, \xE4\xBA\xAB\xE5\x8F\x97\xE7\x94\x9F\xE6\xB4\xBB\"}"
p JSON(data)
# >> {"id"=>2141219585, "class"=>1, "name"=>"zzhang_张哲", "description"=>"程序员, 苹果产品重度依赖者, 爱游戏，爱运动, 享受生活"}

There are two things I noticed: 1. with json parse I got the encoding right; 2. JSON(data) just works.

1. how JSON change the encoding of data

require 'json'
data =  "{\"id\":2141219585,\"class\":1,\"name\":\"zzhang_\xE5\xBC\xA0\xE5\x93\xB2\",\"description\":\"\xE7\xA8\x8B\xE5\xBA\x8F\xE5\x91\x98, \xE8\x8B\xB9\xE6\x9E\x9C\xE4\xBA\xA7\xE5\x93\x81\xE9\x87\x8D\xE5\xBA\xA6\xE4\xBE\x9D\xE8\xB5\x96\xE8\x80\x85, \xE7\x88\xB1\xE6\xB8\xB8\xE6\x88\x8F\xEF\xBC\x8C\xE7\x88\xB1\xE8\xBF\x90\xE5\x8A\xA8, \xE4\xBA\xAB\xE5\x8F\x97\xE7\x94\x9F\xE6\xB4\xBB\"}"
p data.encoding
p JSON(data)["name"].encoding



module JSON
  class << self
    def parse(source, opts = {})
      Parser.new(source, opts).parse
    end
  end
end


source = convert_encoding(StringValue(source));


2. how just JSON(data) works(a little bit wried, isn't it?).
Using `method(:JSON).source_location` I got
["~/.rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/json/common.rb", 417]
And there found out:

module ::Kernel
  private
  # If _object_ is string-like, parse the string and return the parsed result as
  # a Ruby data structure. Otherwise, generate a JSON text from the Ruby data
  # structure object and return it.
  #
  # The _opts_ argument is passed through to generate/parse respectively. See
  # generate and parse for their documentation.
  def JSON(object, *args)
    if object.respond_to? :to_str
      JSON.parse(object.to_str, args.first)
    else
      JSON.generate(object, args.first)
    end
  end
end

Seems json defines method `JSON` into Kernel, and make it can both parse or generate json data depending on data respond_to. Through it a little bit wired names the method `JSON`.(Shouldn't we name methods with downcase and constants with upcase in Ruby?)

###3. textmate 运行结果和命令行不同

s = "\xE7\xA8\x8B\xE5\xBA\x8F\xE5\x91\x98, \xE8\x8B\xB9\xE6\x9E\x9C\xE4\xBA\xA7\xE5\x93\x81\xE9\x87\x8D\xE5\xBA\xA6\xE4\xBE\x9D\xE8\xB5\x96\xE8\x80\x85, \xE7\x88\xB1\xE6\xB8\xB8\xE6\x88\x8F\xEF\xBC\x8C\xE7\x88\xB1\xE8\xBF\x90\xE5\x8A\xA8, \xE4\xBA\xAB\xE5\x8F\x97\xE7\x94\x9F\xE6\xB4\xBB"

p s.encoding
p s

Above code run in textmate(using ruby bundle, with command + R)Got

#<Encoding:UTF-8>
"程序员, 苹果产品重度依赖者, 爱游戏，爱运动, 享受生活"

But when run in term, with ruby file_name.rb

Got
#<Encoding:ASCII-8BIT>
"\xE7\xA8\x8B\xE5\xBA\x8F\xE5\x91\x98, \xE8\x8B\xB9\xE6\x9E\x9C\xE4\xBA\xA7\xE5\x93\x81\xE9\x87\x8D\xE5\xBA\xA6\xE4\xBE\x9D\xE8\xB5\x96\xE8\x80\x85, \xE7\x88\xB1\xE6\xB8\xB8\xE6\x88\x8F\xEF\xBC\x8C\xE7\x88\xB1\xE8\xBF\x90\xE5\x8A\xA8, \xE4\xBA\xAB\xE5\x8F\x97\xE7\x94\x9F\xE6\xB4\xBB"

In textmate Bundles/Edit Bundle:

#!/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby -KU
ENV['RUBYLIB'] = "#{ENV['TM_BUNDLE_SUPPORT']}/RubyMate"
require "#{ENV['TM_BUNDLE_SUPPORT']}/RubyMate/run_script.rb"


seems it call ruby with option: -KU, which means use utf-8 as default coding way.


###4. in irb works too
how irb know the encoding

irb -E "US-ASCII"


ascii, utf一一对应

也行
# encoding: UTF-8 will use the set encoding in current file, not work with content from outside



p "\xE7\xA8\x8B\xE5\xBA\x8F\xE5\x91\x98, \xE8\x8B\xB9\xE6\x9E\x9C\xE4\xBA\xA7\xE5\x93\x81\xE9\x87\x8D\xE5\xBA\xA6\xE4\xBE\x9D\xE8\xB5\x96\xE8\x80\x85, \xE7\x88\xB1\xE6\xB8\xB8\xE6\x88\x8F\xEF\xBC\x8C\xE7\x88\xB1\xE8\xBF\x90\xE5\x8A\xA8, \xE4\xBA\xAB\xE5\x8F\x97\xE7\x94\x9F\xE6\xB4\xBB".encode("UTF-8")


# encoding: utf-8
comment works


__ENCODING__.name

http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html
http://about.ac/2012/06/understanding-m17n.html
http://graysoftinc.com/character-encodings/ruby-19s-three-default-encodings



the magic comment format rules are pretty loose and all of following examples would work the same:

copied
# encoding: UTF-8

# coding: UTF-8

# -*- coding: UTF-8 -*-



# encoding: UTF-8 的作用

and include??

magic-encoding

Before processing your source code interpreter reads this line and sets proper encoding


Since Ruby 1.9, Strings always have an encoding attached. So Ruby can properly handle multi-byte characters and is able to convert between different encodings. Prior versions of Ruby basically handled strings as byte arrays which made it nearly impossible to properly handle multiple encodings.

By default, Ruby 1.9 uses US_ASCII encoding everywhere. But you can change that. There are three different default encodings you can set (which all use US_ASCII by default:

internal encoding: The default encoding all strings are converted to. This is the encoding that strings are saved internally.
external encoding: When reading files, assume them to be in that encoding.
source encoding: Assume the ruby source code to be written in this encoding
The former two encodings can be set like this

Encoding.default_internal = 'UTF-8'
Encoding.default_external = 'UTF-8'
They are then used during all operations in the current Ruby processes lifetime.

The source encoding can be set using a "magic comment" on the first line of your ruby file (or below the shebang) like so

# encoding: UTF-8

http://ruby-doc.org/core-1.9.3/Encoding.html




http://www.ruby-doc.org/core-1.9.3/String.html#method-i-encode


很重要的一点需要注意，在这里我可以作此操作是因为这些字节在 US-ASCII 和 UTF-8 编码中是一样的。我并没有改变数据，只是改变了处理这些数据的规则。


As for encoding defaults:

Ruby 1.8 and below didn't knew the concept of string encodings at all. Strings were more or less byte arrays.
Ruby 1.9: default string encoding is US_ASCII everywhere.
Ruby 2.0 and above: default string encoding is UTF-8.