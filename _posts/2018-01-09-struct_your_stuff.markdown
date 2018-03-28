---
layout: post
title:      "Struct Your Stuff"
date:       2018-01-09 19:17:27 -0500
permalink:  struct_your_stuff
---


The `Struct` class is a quick way to create a class object without having to write out the initialize method or attribute accessors. Struct should be used when you have a simple object model with a known set of attributes. While arrays and hashes also provide simple ways to structure data, the `Struct` class offers an opportunity to create cleaner code by encapsulating attributes into a class.

<img src="https://i.imgur.com/nqTnKNU.png" title="source: imgur.com" height="300" width="300" class="img-responsive">

## What about Arrays and Hashes?
Let's say you are working at a vet's office, and you want to keep track of each cat by name, owner name, and age. Each array within the cats array below represents each cats's name, owner name, and age.

```ruby
cats = [["John", "Eric", 1], ["Sal", "Janice", 3]]
```

Hmm. Some of these cats have human-like names, so it is tough to keep track of which name belongs to the human and which name belongs to the cat. Perhaps using a hash will structure our data in a way that allows us to differentiate between the attributes.

```ruby
cats = [
  {name: "John", owner_name: "Eric", age: 1},
  {name: "Sal", owner_name: "Janice", age: 4},
]
```

Obviously, hashes help us more easily differentiate between the attributes. However, a developer that missed their morning coffee decides to add new functionality that allows new cats to be added:

```ruby
cats << {name: "Flanders", owner_name: "Bart", ageeee: 4}
```

As you may have noticed, the lack of coffee led the developer to make a typo. Unfortunately since you
can add any key value pair to a hash that you like, the developer's typo does not cause an error to be generated, and you're left with a corrupted CatDB.

<img src="https://i.imgur.com/br0peQq.png" title="source: imgur.com" height="300" width="300" class="img-responsive">

```ruby
cats => [
  {name: "John", owner_name: "Eric", age: 1},
  {name: "Sal", owner_name: "Janice", age: 4},
  {name: "Flanders", owner_name: "Bart", ageeee: 4},
]
```

## Struct to the Rescue

To avoid these errors, we'll create a `Cat` class to define and encapsulate the cat attributes.

```ruby
class Cat
 attr_accessor :name, :owner_name, :age

  def initialize(name, breed, age)
    @name = name
    @breed = breed
    @age = age
  end
end
```
Doing this once is simple and easy, but after the 5th class like this, you may be looking for a better way. Luckily, Ruby has a tool built in for exactly this situation. Because we need a simple class, and we are confident the attributes won't change a lot, we'll use the handy dandy `Struct` class. This is a quick way to organize attributes together into a class

### Creating a class object with Struct

```ruby
Cat = Struct.new(:name, :owner_name, :age)
  ```

That's pretty much it. If you want to add some instance methods, you can use the block syntax:

```ruby
Cat = Struct.new(:name, :owner_name, :age) do
  def name_with_age
    "Name: #{self.name} - Age: #{self.age}"
  end
end
```

### Using your Cat class

As you can see, `Struct` is much more concise. We can now model each cat and easily access attributes.

```ruby
cat_1 = Cat.new("John", "Eric", 1)
cat_2 = Cat.new("Sal", "Janice", 3)

cat_1.name
=> "John"

cat_2.name
=> "Sal"

cat_1.ageee = 25
# => NoMethodError: undefined method `ageee=' for #<struct Cat name="John", owner_name="Eric", age=1>
```

You don't have to use `Struct`, but it might make your life easier. I used `Struct` to create a data structure for my [basic Sinatra app](https://github.com/hcarnes/sinatra-basic-forms-lab-v-000/blob/master/models/puppy.rb). For understanding when to use a `Hash` instead of a `Struct`, I found [this](https://stackoverflow.com/a/3275624) StackOverflow answer very helpful.

