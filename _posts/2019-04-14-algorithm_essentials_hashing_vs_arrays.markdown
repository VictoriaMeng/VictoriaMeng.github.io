---
layout: post
title:      "Algorithm Essentials: Hashing vs Arrays"
date:       2019-04-15 01:37:00 +0000
permalink:  algorithm_essentials_hashing_vs_arrays
---


Let's say you're given the below problem:

`Given an array of N positive integers, find the smallest integer that isn't included in the array.`

A beginner programmer might write a loop that iterates over the array until it finds the smallest integer, like below (Example code in Ruby).

```
def smallest(my_array)
  memo = 1
	until my_array.none? { | number | memo == number }
	  memo +=1 
	end
	memo
end
```

But this is not an efficient algorithm, quite slow at handling large inputs. The above algorithm needs to iterate over the array multiple times, parsing array elements that it has parsed before, before finding the smallest missing positive integer. If the array has thousands of elements, this is very inefficient. The time complexity would be O(N^2).

A better way is to iterate over the array once to create a hash table, and then look up the missing value from the table.

```
def smallest(my_array)
  my_hash = {}
  my_array.each { | number | my_hash[number] = number }
	memo = 1
	until !my_hash[memo]
	  memo +=1
	end
	memo
end
```

The time complexity of the above code would only be O(n).
