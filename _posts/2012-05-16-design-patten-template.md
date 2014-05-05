---
layout: post
title: "设计模式之'模版方法'"
categories: 设计模式学习
---


#模式描述
`模版方法` 是一种行为型模式。
（行为型模式描述类或对象如何交互及如何分配职责，它主要涉及通过合理的处理方法，达到使系统升级性和维护性提高的目的。）

基本描述：在一个方法里定义算法的骨架，将具体实现方法延迟到其子类。一次性实现算法的不变部分，将可变的行为留给子类。

更多描述：通过继承把变和不变的部分分开。抽象类应当拥有的是行为组合和尽可能少的具体实现。算法骨架类的公共行为被提取出来集中到父类，要扩展新的功能，就创建新的子类并实现相应功能。

***

<br>
#实例1：各种格式的报表生成

案例描述：报表包括 开头、主干、结尾，但需要各种格式，如xml, yaml, json等。实现各类报表，要求各部分内容一致，但是输出格式不同。
例如：


    文本格式:
    head: "hello"
    body: "keep it simple"
    end: "I am strong"

    xml格式:
    <head>
      hello
    </head>
    <body>
      keep it simple
    </body>
    <end>
      I am strong
    </end>

    yaml格式:
    head: hello
    body: keep it simple
    end: I am strong

    json格式:
    {"head": "hello", "body": "keep it simple", "end": "I am strong"}


代码实现：

{% highlight ruby %}

class Report
  def initialize(head, body, r_end)
    @head = head
    @body = body
    @end = r_end
  end

  def output
    "#{output_head}#{output_body}#{output_end}"
  end
end

class TextReport < Report
  def output_head
    "head: #{@head}\n"
  end

  def output_body
    "body: #{@body}\n"
  end

  def output_end
    "end: #{@end}"
  end
end

class XmlReport < Report
  def output_head
    "<head>#{@head}</head>\n"
  end

  def output_body
    "<body>#{@body}</body>\n"
  end

  def output_end
    "<end>#{@end}</end>"
  end
end

class JsonReport < Report
  def output_head
    %Q{\{"head": "#{@head}", }
  end

  def output_body
    %Q{"body": "#{@body}", }
  end

  def output_end
    %Q{"end": "#{@end}"\}}
  end
end

describe "TextReport" do
  it "should output well" do
    TextReport.new("hello", "keep it simple", "I am strong").output.should ==
"head: hello\nbody: keep it simple\nend: I am strong"
  end
end

describe "XmlReport" do
  it "should output well" do
    XmlReport.new("hello", "keep it simple", "I am strong").output.should ==
"<head>hello</head>\n<body>keep it simple</body>\n<end>I am strong</end>"
  end
end

describe "JsonReport" do
  it "should output well" do
    JsonReport.new("hello", "keep it simple", "I am strong").output.should ==
'{"head": "hello", "body": "keep it simple", "end": "I am strong"}'
  end
end

{% endhighlight %}

<br>

***

<br>

#实例2：制作电子产品
案例描述：电子产品的生产都要包括硬件、软件的设计和集成起来的调试步骤。但各具体产品的软硬件的具体生产方式和调试方法各不相同。

{% highlight ruby %}

class ElectronicProduct
  def generate
    generate_hardware
    generate_software
    debug
  end

  private
  def generate_hardware
    p "generate_hardware"
  end

  def generate_software
    p "generate_software"
  end

  def debug
    p "debug"
  end
end

class Iphone < ElectronicProduct
  private
  def generate_hardware
    p "generate_hardware for Iphone"
  end

  def generate_software
    p "generate_software for Iphone"
  end

  def debug
    p "debugging Iphone"
  end
end

Iphone.new.generate

class Ipad < ElectronicProduct
  private
  def generate_hardware
    p "generate_hardware for Ipad"
  end

  def generate_software
    p "generate_software for Ipad"
  end

  def debug
    p "debugging Ipad"
  end
end

Ipad.new.generate

{% endhighlight %}

<br>

***


总结：模板方法实际上是利用多态这种晚绑定机制来将一些执行动作的定义延迟到子类。子类可以在不改变算法结构的情况下，调整算法的细节实现。
除了可以灵活应对子步骤的变化外，“不用调用我，让我来调用你”的反向控制结构是模版方法的典型应用。

