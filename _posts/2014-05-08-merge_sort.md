---
layout: post
title: "插入排序ruby实现"
categories: 算法导论学习
---

{% highlight ruby %}
class Array
  def insert_sort
    array = self
    result = [self[0]]
    (1 .. (array.size - 1)).each do |e|
      result.insert_olderly!(array[e])
    end
    result
  end

  protected
  def insert_olderly!(k)
    self.replace(insert_olderly(k))
  end

  def insert_olderly(k)
    array = self
    result = []
    (0 .. (array.size - 1)).each do |i|
      if array[i] > k
        result << k
        result = result + array[i .. array.size-1]
        break
      else
        result << array[i]
        result << k if (i == array.size - 1)
      end
    end
    result
  end
end
{% endhighlight %}