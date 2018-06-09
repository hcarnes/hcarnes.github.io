---
layout: post
title:      "A bunch of ways to reverse a string in Ruby"
date:       2018-06-06 15:12:15 -0500
permalink:  reverse_string
---

This post will show you 5 ways to reverse a string in Ruby. In production code, you would always want to use the `reverse!` method because it is easier and faster. By showing how to manually reverse a string, you are demonstrating your knowledge the string data type, which is core to any programming language. Here are some string concepts you should know before attempting to reverse a string:

* A string is a sequence of one or more characters that may consist of letters, numbers, or symbols.
* Like arrays, where each element corresponds to an index number, each of a string’s characters also correspond to an index number,    starting with the index number 0.
* You can concatenate strings to combine individual strings into one string. Here is an example:

 ```ruby
  "h" + "e" + "l" + "l" + "o"

  => "hello"
 ```

Here is some pseudocode that provides the backbone for all approaches discussed in this post:

* String is an input to a function
* Convert the string to an array
* Create an empty array to store the new array of reversed characters
* Iterate over each character in the array and prepend each character in front of the array.
* Convert the array of reversed characters back to a string
* Return the reversed string

Because this post is meant to teach you how to reverse a string, as opposed to simply showing different approaches, the first example is explicit and shows comments and return values for each step. After the first example, I'll show some cleaner approaches and provide insight on how those work as well. Let's dig in.

```ruby

string = "hello"

def reverse_string(string)
  char_array = string.split('')
end

=> ["h", "e", "l", "l", "o"]

def reverse_string(string)
  char_array = string.split('')
  # Create an empty array to store the new array of reversed characters
  reversed_string = []
  # Iterate over each character in the array and prepend each character in front of the array.
  char_array.each do |char|
    reversed_string.unshift(char)
  end

  reversed_string
end

=> ["o", "l", "l", "e", "h"]

def reverse_string(string)
  char_array = string.split('')
  reversed_string = []
  
  char_array.each do |char|
    reversed_string.unshift(char)
  end
  # Convert the array of reversed characters back to a string
  # Return the reversed string
  reversed_string.join('')
end

=> "olleh"
```

`unshift` is an array method that takes its arguments and prepends it to the front of array. In the example of above, for each character in the `char_array`, the character gets bumped back using `unshift` to make room for the new `char`. If we add `puts reversed reversed_string.join('')` inside the `each` block, we can see what is happening under the hood.

```ruby

def reverse_string(string)
  char_array = string.split('')
  reversed_string = []
  
  char_array.each do |char|
    reversed_string.unshift(char)
    puts reversed_string.join('')
  end

  reversed_string.join('')
end

reverse_string(string)

h
eh
leh
lleh
olleh
=> "olleh"
```

Each of a string’s characters also correspond to an index number, starting with the index number 0. With that knowledge, we use a `while` loop to execute a block that will bumping the value of the last index of they word to the front of the array until the word is reversed.

```ruby
string = "hello"

def reverse_string(string)
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

reverse_string(string)

o
ol
oll
olle
olleh
=> "olleh"
```

amazing!

```ruby
 def reverse_string(string)
    half_length = string.length / 2
    half_length.times do |index| 
      string[index], string[-index-1] = string[-index-1], string[index]
    end
    string
  end

reverse_string("hello")

=> "olleh"
```

When reversing characters in a word, you are alo putting the characters in inverse order starting from the first and last and working your way. 

```ruby
 def reverse_string(string)
    half_length = string.length / 2
    half_length.times do |index| 
      string[index], string[-index-1] = string[-index-1], string[index]
      puts "string[index] = " + string[index]
      puts "string[-index-1] = "+ string[-index-1]
    end
    string
  end

reverse_string("hello")

string[index] = o
string[-index-1] = h
string[index] = l
string[-index-1] = e
=> "olleh"
```

Here is an even cleaner approach using `reduce`.

```ruby
  string = "hello"
  
  def reverse_string(string)
    string.chars.reduce { |memo, element| element + memo }
  end

  => "olleh"
```
It helps to think of `reduce` as taking an array and "reducing" it down to one accumulator value, which is also known as a the `memo` in Ruby (short for memory). The first argument is the `memo` and the second argument represents each element of the array. The block `element + memo` sets how the elements of the array will be reduced back to one value. In this example, the block gets executed once for each letter of the string `"hello"` and return the `memo` which is all the letters combined. To reverse a string, we place `element` in front of `memo` so that at each iteration, the next element will be moved in fron of the `memo`. If the block was `memo + element` then the string wouldn't be reversed.

```ruby
  string = "hello"
  
  def reverse_string(string)
    string.chars.reduce { |memo, element| memo + element }
  end

  => "hello"
```

This could also look like this:

```ruby
def reverse(a)
  (0...(a.length/2)).each {|i| a[i], a[a.length-i-1]=a[a.length-i-1], a[i]}
end
```

In Ruby, an alias to `reduce` is the `inject` method, and you can get the same results with this method.

```ruby
  string = "hello"
  
  def reverse_string(string)
    string.chars.inject { |x, y| y + x }
  end
  => "olleh"
```

There are even more ways to manually reduce an array. Just in this post, I showed you how to use methods `chars` and `split` to convert strings to array. This post also explore how different array methods like `unshift`, `reduce`, `inject` and help mutate an array to your liking. Finally, you can see how iterators like `each` and `times` could help get the job done. And, don't for getting about the `while` loop. Wow, so many options!






 
 


