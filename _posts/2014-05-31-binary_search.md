---
layout: post
title: "二分查找的ruby实现"
categories: 算法导论学习
---

{% highlight ruby %}
class Array
  def binary_search_index(e, from=0)
    middle_index = (self.size - 1)/2
    if self[middle_index]
      first_half = self[0 .. (middle_index - 1)]
      last_half = self[(middle_index + 1) .. -1]

      if self[middle_index] > e
        first_half.binary_search_index(e, from)
      elsif self[middle_index] < e
        last_half.binary_search_index(e, from + middle_index + 1)
      else
        return from + middle_index
      end
    end
  end

  def binary_search_index_loop(e)
    from = 0
    to = self.size - 1

    while from <= to
      middle_index = (from + to)/2
      if self[middle_index] == e
        return middle_index
      elsif self[middle_index] > e
        to = middle_index - 1
      elsif self[middle_index] < e
        from = middle_index + 1
      end
    end
    return nil
  end
end
{% endhighlight %}

循环版本要比迭代版本快3-4倍。