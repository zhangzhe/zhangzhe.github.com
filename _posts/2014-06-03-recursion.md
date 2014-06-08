---
layout: post
title: "递归算法的常用应用案例的ruby实现"
categories: 算法导论学习
---

# 乘方的递归实现

循环乘法的时间复杂度为O(n), 递归算法时间复杂度为 O(lg(n))。

{% highlight ruby %}
class Fixnum
  def recurse_power(n)
    if n == 1
      self
    elsif n == 0
      1
    elsif n.even?
      self.recurse_power(n/2) * self.recurse_power(n/2)
    elsif n.odd?
      self.recurse_power(n/2) * self.recurse_power(n/2) * self
    end
  end

  def power(n)
    result = 1
    n.times do
      result *= self
    end
    result
  end
end
{% endhighlight %}


<br><br>

# fibonacci的递归实现

下面列举了三种实现，其中递归实现因为太多次重复计算性能极差，可以通过缓存计算结果或者bootom_up的方式解决性能问题，后两者的时间复杂度皆为：O(n)。

{% highlight ruby %}
class Fixnum
  def fibonacci_recurse
    if self == 1
      0
    elsif self == 2
      1
    else
      (self - 1).fibonacci_recurse + (self - 2).fibonacci_recurse
    end
  end

  def fibonacci_bottom_up
    if self == 1
      0
    elsif self == 2
      1
    else
      result = [0, 1]
      (3 .. self).each do |i|
        result << result[-1] + result[-2]
      end
    end
    result.last
  end

  def fibonacci_recurse_with_cache
    @result ||= []
    if @result[self]
      return @result[self]
    else
      if self == 1
        @result[self] = 0
      elsif self == 2
        @result[self] = 1
      else
        @result[self] = (self - 1).fibonacci_recurse_with_cache +
        (self - 2).fibonacci_recurse_with_cache
      end
      return @result[self]
    end
  end
end
{% endhighlight %}

<br><br>

# 二分查找

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
end
{% endhighlight %}

<br><br>

# 合并排序


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

<br><br>

#快速排序

{% highlight ruby %}
class Array
  def quick_sort
    return self if self.size <= 1
    left = []
    right = []
    pivots = []
    pivot = self.sample
    self.each do |e|
      if e < pivot
        left << e
      elsif e > pivot
        right << e
      else
        pivots << e
      end
    end
    return (left.quick_sort + pivots + right.quick_sort)
  end
end
{% endhighlight %}


