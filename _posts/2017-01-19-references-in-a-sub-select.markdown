---
author: al
date: 2017-01-19 21:14:25+00:00
layout: post
title: References in a sub-select
categories:
- /dev/null
---

Have you ever had a sub-select where you really needed to reference a value in the outer query?  I know I have!  The naive way would be to run the outer query and then loop over the results running the inner query on each one.  Luckily, there's a better way.  The [Correlated subquery](https://en.wikipedia.org/wiki/Correlated_subquery).  Check it out!  The example given is 

{% highlight sql %}
SELECT employee_number, name
  FROM employees AS Bob
  WHERE salary > (
    SELECT AVG(salary)
      FROM employees
      WHERE department = Bob.department);
{% endhighlight %}

See how the sub-select references the outer query?  It's SQL magic.
