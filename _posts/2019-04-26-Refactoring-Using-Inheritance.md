---
layout: post
title: Refactoring a really bad rails serializer.
---

I happened to come across some code today that used 
ActiveModel Serializers to expose a particular resource
via an API.

Unfortunately, based on whether the API call was internal or
not, we needed to delete an attribute. The resulting code looked
like the following.
```ruby
Class ObjectSerializer < ActiveModel::Serializer
  attributes :internal_param, :external_param
  
  def internal_param
    object.internal_param
  end
  
  def external_param
    object.external_param
  end
  
  def filter(keys)
  keys.delete(:internal_param) if external_call?
  end
  
  private
  
  def external_call?
    !object.internal_call?
  end
end
```

What immediately struck me was the presence of the filter
method with the ugly `if` statement. What further complicated
matters was the fact that whether the call was internal or not
had nothing to do with the object being serialized. It was being
forcibly tacked on to the object at the controller level.

I decided to see if I could use composition to refactor this 
a bit. I ended up creating the following two classes.

```ruby
Class InternalSerializer < ActiveModel::Serializer
  attributes :internal_param, :external_param
  
  def internal_param
    object.internal_param
  end
  
  def external_param
    object.external_param
  end
end
```

```ruby
Class ExternalSerializer < ActiveModel::Serializer
  attributes :external_param
  
  def external_param
    object.external_param
  end
end
```

I then pushed the responsibility for deciding which serializer to use
up to the controller where it belonged.

```ruby
SomeController...
...

  serializer = internal_api_call? ? InternalSerializer : ExternalSerializer
  
  render json: resource, serializer: serializer
    
end
```

The reason I didn't have `InternalSerialzer` inherit from
`ExternalSerializer` is because logically these classes do not
have anything in common and can evolve independently.

Do you think this refactoring made the code better? Let me know.

