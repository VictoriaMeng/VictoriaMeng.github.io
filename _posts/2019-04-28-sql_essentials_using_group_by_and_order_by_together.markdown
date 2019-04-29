---
layout: post
title:      "SQL Essentials: Using GROUP BY and ORDER BY Together"
date:       2019-04-29 02:43:33 +0000
permalink:  sql_essentials_using_group_by_and_order_by_together
---


In SQL, the main difference between the `GROUP BY()` and `ORDER BY()` functions is that `ORDER BY()` sorts returned values by column name, in ascending or descending order, while `GROUP BY()` sorts the result sets of aggregate functions like `sum`, `count`, `minimum`, or `maximum`.

Imagine a table of `cats` with a `name,` `age`, and `color` column. You would write a query to order the list by `name` alphabetically like this:

`"SELECT * FROM cats ORDER BY name ASC"`

While you would return the number of cats older than 5 years old in ascending order by name like this:

`"SELECT name, COUNT(age) FROM cats WHERE age >= '5' GROUP BY name ASC"`

Combined, `ORDER BY()` and `COUNT BY()` can perform more complex queries, such as when you want to select the most common color and return with the count:

`"SELECT color, COUNT(color) FROM cats GROUP BY color ORDER BY COUNT(color) DESC LIMIT 1"`

