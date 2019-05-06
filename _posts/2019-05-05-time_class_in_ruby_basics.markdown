---
layout: post
title:      "Time Class in Ruby: Basics"
date:       2019-05-06 02:33:43 +0000
permalink:  time_class_in_ruby_basics
---

## In case someone asks you to use some basic Time objects on a technical interview, you will now be prepared!

Instances of the Ruby `Time` class can be used to represent a *date* and *time*, like below.

```
t = Time.now
=> 2019-05-05 22:09:51 -0400
```

The *date* has three components: Day, month, and year.

The *time* also has three components: Hours, minutes, and seconds.

The day, month, year, and hour components have corresponding method that can be passed into the `Time` instance to retrieve only that component.

```
t = Time.now
=> 2019-05-05 22:09:51 -0400

t.day
=> 5

t.month
=> 5

t.year
=> 2019

t.hour
=> 22

t.min
=> 09

t.sec
=> 51
```

There is also a `.zone` method that returns the time zone as a string.

```
t.zone
=> "Eastern Daylight Time"
```

You can also check to see if a time object matches a specific day of the week.

```
t.sunday?
=> true

t.monday?
=> false
```

To convert a time object to a string, simply call `.to_s` like you would a float or integer.

```
t = Time.now
=> 2019-05-05 22:09:51 -0400

t.to_s
=> "2019-05-05 22:14:12 -0400"
```

And remember, strings in Ruby can be compared with comparison operators. This is a useful way to compare dates if you're working with a program that parses dates into a string format.

```
"2019-05-05 22:14:12 -0400" > "2019-01-01"
=> true

"2019-05-05 22:14:12 -0400" < "2019-12-12"
=> false
```




