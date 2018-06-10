---
layout: post
title:      "A bunch of ways to reverse a string in Ruby"
date:       2018-06-06 15:12:15 -0500
permalink:  reverse
---

I'm going to show you a bunch of ways to reverse a string in Ruby. Normally, you would want to use the `reverse` method because it's faster. However, if you know how to manually reverse a string, you can demonstrate your knowledge of the string data type, which is core to any programming language.

## Pseudocode

To begin, let's outline pseudocode for our first approach:

* String is an input to a function
* Convert the string to an array
* Create an empty array to store the new array of reversed characters
* Iterate over each character in the array and prepend each character in front of the array
* Convert the array of reversed characters back to a string
* Return the reversed string

This first example is explicit and shows comments and return values for each step. After the first example, I'll show some cleaner approaches and provide insight on how those work as well.

## Let's iterate

```ruby

string = "hello"

# String is an input to a function
def reverse(string)
  # Convert the string to an array
  array = string.split('')
end

reverse(string)

=> ["h", "e", "l", "l", "o"]

def reverse(string)
  array = string.split('')
  # Create an empty array to store the new array of reversed characters
  reversed_string = []
  # Iterate over each character in the array and prepend each character in front of the array
  array.each do |char|
    reversed_string.unshift(char)
  end
end

reverse(string)

=> ["o", "l", "l", "e", "h"]

def reverse(string)
  array = string.split('')
  reversed_string = []
  
  array.each do |char|
    reversed_string.unshift(char)
  end
  # Convert the array of reversed characters back to a string
  # Return the reversed string
  reversed_string.join('')
end

reverse(string)

=> "olleh"
```

`unshift` is an array method that takes its arguments and prepends it to the front of array. In the example of above, for each character in the `array`, the character gets bumped back using to make room for the new `char`. If we add `puts reversed reversed_string.join('')` inside the `each` block, we can see what is happening under the hood.

```ruby
def reverse(string)
  array = string.split('')
  reversed_string = []
  
  array.each do |char|
    reversed_string.unshift(char)
    puts reversed_string.join('')
  end

  reversed_string.join('')
end

reverse(string)

h
eh
leh
lleh
olleh
=> "olleh"
```

Here another similar example using a `for loop`:

```ruby
string = "hello"

def reverse(string)
  array = string.split('')
  reversed_string = []
  for char in array
    reversed_string.unshift(char)
  end

  reversed_string.join('')
end

reverse(string)
```

Each of a string’s characters also correspond to an index number, starting with the index number 0. With that knowledge, we use a `while` loop to execute a block that bumps the value of the last index of the string to the front of the array until the string is reversed.

```ruby
string = "hello"

def reverse(string)
  # find the length of the string
  string_length = string.length
  # create a new variable equal to an empty string
  reversed_string = ''
  # create a loop that will execute until each character of the word has been reversed
  while string_length > 0
    # reduce string length by 1 to get the index of the last character of the array
    string_length -= 1
    # add the last character to the new empty string
    reversed_string += string[string_length]
    # show return value after each block is executed
    puts reversed_string
  end
  
  # return final reversed string
  reversed_string
end

reverse(string)

o
ol
oll
olle
olleh
=> "olleh"
```

## Let's invert the index

When reversing characters in a word, you are also assiging each character and inverse index value until all characters have been switched. Two characters are switched when each block executes, so if you calculate half the length of the string, you will have the number of times the block should execute. The method below give each character an inverse index value and return the new string.

```ruby
 def reverse(string)
    half_length = string.length / 2
    half_length.times do |index| 
      string[index], string[-index-1] = string[-index-1], string[index]
    end
    string
  end

reverse("hello")

=> "olleh"
```
```ruby
 def reverse(string)
    half_length = string.length / 2
    half_length.times do |index| 
      string[index], string[-index-1] = string[-index-1], string[index]
      puts "string[index] = " + string[index]
      puts "string[-index-1] = "+ string[-index-1]
    end
    string
  end

reverse("hello")

string[index] = o
string[-index-1] = h
string[index] = l
string[-index-1] = e
=> "olleh"
```
## Let's use a handy array method

Here is an even cleaner approach using `reduce`.

```ruby
  string = "hello"
  
  def reverse(string)
    string.chars.reduce { |memo, char| char + memo }
  end

  => "olleh"
```
Think of `reduce` as taking an array and "reducing" it down to one accumulator value, which is also known as a the `memo` in Ruby (short for memory). The first argument is the `memo` and the second argument represents each element of the array. In this example, the block gets executed once for each letter of the string `"hello"` and will return the `memo`, which is all of the characters combined. To reverse a string, `char + memo` ensures that at each iteration, the next element will be moved in front of the `memo`. If the block was `memo + char`, the string wouldn't be reversed.

## Wow, so many options!

<img src="https://i.imgur.com/eRq68d0.png" title="blue_cat" height="300" width="300" class="img-responsive">

There are even more ways to manually reduce an array. Just in this post, I showed you how to use methods `chars` and `split` to convert strings to array. This post also explore how different array methods like `unshift`, `reduce`, `inject` and help mutate an array to your liking. Finally, you can see how iterators like `each` and `times` could help get the job done. And, don't for getting about the `while loop` and `for loop`. 






 
 


