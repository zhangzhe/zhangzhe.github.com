---
layout: post
title: "合并排序ruby实现"
categories: 算法导论学习
---

{% highlight ruby %}
class Array
  def merge_sort
    array = self
    if array.size > 2
      a1 = array.first_half.merge_sort
      a2 = array.last_half.merge_sort
      a1.merge_with_order(a2)
    elsif array.size == 2
      if array.first < array.last
        array
      else
        array.reverse
      end
    else
      array
    end
  end

  protected
  def merge_with_order(array)
    array_1 = self
    array_2 = array
    result = []
    size = array_1.size + array_2.size
    size.times do
      if array_1.first < array_2.first
        result << array_1.delete_at(0)
      else
        result << array_2.delete_at(0)
      end
      if array_1.size == 0
        result += array_2
        break
      end
      if array_2.size == 0
        result += array_1
        break
      end
    end
    result
  end

  def first_half
    self[0, self.size/2]
  end

  def last_half
    self[self.size/2, self.size]
  end
end
{% endhighlight %}