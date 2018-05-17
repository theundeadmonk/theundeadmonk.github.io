---
layout: post
title: Get rid of conditionals with the null object pattern.
---

#Get rid of conditionals with the null object pattern

You look at a particular codebase and the following piece of code catches your attention.
```ruby
def get_name(employee)
  if employee.nil?
    ''
  else
    employee.name
end
```
