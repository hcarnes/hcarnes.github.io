---
layout: post
title:      "Recursion Excursion"
date:       2018-06-14 15:12:15 -0500
permalink:  recursion_excursion
---

Recursion is the computer science concept of solving a problem where the solution depends on solutions to other instances of the same problem. With recursion, you can take a complex problem with multiple repeating steps, break it down into one small problem, and then feed the solutions of the small problems back into the original function. In order to avoid an infinite loop, you include a base case that signals your function to exit the loop and begin taking all of the solutions to the smaller problems and unwinding them to reach your final solution.

<img src="https://i.imgur.com/eRq68d0.png" title="blue_cat" height="250" width="250" class="img-responsive">

Don't let it overwhelm you! This post will take you through a couple of recursion examples in Ruby and then discuss the pros and cons of iteration versus recursion in Ruby. 

## The Fibonacci Example

For all recursive functions, you need:

1. A base case
2. A process that will get you to the end goal

Let's explore the Fibonnaci algorithm. The Fibonacci sequence is a mathematical construct of a group of integers where each number is the sum of the previous two. Here is the problem: **Implement a function which returns the nth number of a Fibonacci sequences with an input n.** 

<img src="https://i.imgur.com/bv3uWAQ.jpg" title="fibo_sequence" height="450" width="450" class="img-responsive">
*Image Source: [TopJavaTutorial](http://www.topjavatutorial.com/java/java-programs/java-fibonacci-series-algorithm/)*

Here is the beginning of a Fibonnaci sequence in an array: 

`[1, 1, 2, 3, 5, 8, 13]`. 

The examples below show you how the algorithm should work:

* `fib(3) => 2`
  * The 3rd value in the sequence is 2. 
  * 1 + 1 = 2
* `fib(4) => 3` 
  * The 4th value in the sequence is 3. 
  * 1 + 2 = 3
* `fib(5) => 3` 
  * The 5th value in the sequence is 5. 
  * 2 + 3 = 5
* `fib(6) => 8` 
  * The 6th value in the sequence is 8. 
  * 3 + 5 = 8

To get the solution, you need keep obtaining the sum of the previous two values until you reach a base case.

Here is the recursive solution:

```ruby
def fib(n)
  if n < 2
    n
  else
    fib(n-1) + fib(n-2)
  end
end

fib(4) => 3
```

In the image below, the black writing shows that when `fib(4)` is called, the function calls `fib(n-1) + fib(n-2)` until the base case `fib(1)` or `fib(0)`is reached. As you can see in the red writing, once the function reaches this endpoint, it winds back up until it can formulate the final solution (`3`).

<img src="https://i.imgur.com/5UFLrTD.jpg" title="fibo_chart" height="450" width="450" class="img-responsive">

## The case for the base case
The block `if n < 2` is the base case of this function. `[0, 1, 1,]` is the beginning of the Fibonacci sequence and is the part of the sequence that no longer depends on the function calls before it to return a solution. Recursive functions need the base case to terminate the loop. If you get the error "stack level too deep", you probably forgot to include the base case. This error is your computer telling you that you have reached your limit on the call-stack, which means you are running an infinite loop.

```ruby
def fib(n)
  fib(n-1) + fib(n-2)
end

fib(6) # SystemStackError (stack level too deep)
```

## A slightly more complex example

Here is a new problem: **Write a function that, given a total amount of money and an array of coins, computes the number 
of ways to make the amount of money with the available coin denominations.** 

Example: for amount = 3 (3¢) and coin denominations=[1,2,3] (1¢, 2¢ and 3¢), your function should return 3 — the number of ways to make 3¢ with those denominations: 

* 1¢, 1¢, 1¢
* 1¢, 2¢,
* 1¢, 3¢

Let's start by defining the base case(s). 

* If the amount is negative, there are 0 solutions because there can't be a negative sum of money.
* If there are no coin denominations available, there are 0 possible solutions.
* If the amount of money is equal to 0, then there is only 1 solution, which would be choosing coin change of 0.

Using Ruby's splat operator (`*`), we can separate the first coin in the `deonominations` array from the remaining coins. Once we have that, we can executive our recursive function until a base case is reached:

For every coin we have 2 options:

* Include the coin: subtract the amount by coin value and use the sub problem solution `(amount - head, denominations)`.
* Exclude the coin: use the solution for the same amount without considering that coin `(amount, tails)`.

The separate solutions must be added together to get the full solution.

```ruby
  amount = 3
  denominations = [1, 2, 3]
  
def changePossibilities(amount, denominations)
  if amount < 0 || denominations.empty?
    0
  elsif amount == 0
    1
  else
    head, *tail = denominations
    changePossibilities(amount, tail) + changePossibilities(amount - head, denominations)
  end
end

changePossibilities(amount, denominations) => 3
```
In the drawing below, the black ink shows that `changePossibilities(amount, denominations]`is called until it reaches a base case, then it winds back up (shown in red) until it can formulate the final solution (3).

<img src="https://i.imgur.com/6bXBRJW.jpg" title="fibo_chart" height="550" width="550" class="img-responsive">
**pos = changePossibilities*

## Recursion Vs. Iteration

You might be thinking that you could solve these problems without recursion, and that is correct. Ruby is an imperative language that has a lot of useful methods for iterating over data and changing the state of that data. For example, the `for` and `each` loops mutate the value of the array. In other words, when you operate on data with Ruby's `for`, the value of the original data changes. For that reason, it often makes sense to use iteration over recursion. Furthermore, in most cases, using iteration is actually [faster](https://stackoverflow.com/questions/3021/what-is-recursion-and-when-should-i-use-it/3093#3093). Some languages, known as functional languages (ie. Elixir, Haskell), don't offer methods that mutate data, which can leave recursion as the only option. 

This [post](https://www.leighhalliday.com/recursion-in-ruby) takes a deeper dive on the topic of recursion in Ruby and shows examples on how you can solve the problems with iteration or recursion in Ruby. 


