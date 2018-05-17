---
layout: post
title: Get rid of conditionals with the null object pattern.
---

You look at a particular codebase and the following piece of code catches your attention.
```ruby
def get_name(employee)
  if employee.nil?
    ''
  else
    employee.name
end
```
Seem familiar?
you find this patter repeated all through the file

```ruby
def calculate_bonus(employee)
  if employee.nil?
    0
  else
    employee.salary * 0.1
end
```

As rubyists, we quickly spot a chance to make this code more readable and we come up with the following solution
```ruby
def get_name(employee)
  return '' if employee.nil?
  employee.name
end

def calculate_bonus(employee)
  return 0 if employee.nil?
  employee.salary * 0.1
end
```

However, the fact that we still have a nil check in every method is a code smell.
We can use the null object pattern to refactor our code and get rid of the nil checks entirely. Lets look at our Employee class.
```ruby
class Employee

  attr_reader :name, :salary
  
  def initialize(name, salary)
    @name = name
    @salary = salary
  end
end
```
What we need to do is to create a class that can fill in for when an employee is nil. To do this, we create a new class that mimics the Employee class. If this were a language like Java, this new class would need to inherit from Employee, but since RUby is duck typed, we need to do no shuch thing. Let's create our new class.

```ruby
class NilEmployee

  def name
    ''
  end
  
  def salary
    0
  end
end
```
