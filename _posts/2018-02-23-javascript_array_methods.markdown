---
layout: post
title:      "JavaScript Array Methods"
date:       2018-02-23 00:01:34 -0500
permalink:  javascript_array_methods
---


JavaScript has data structures (like arrays!) and you can operate on arrays with different methods. There are subtle differences between common methods that might make your head spin if you don't understand the nitty gritty details. 

<img src="https://i.imgur.com/8NwhIUG.gif" title="source: imgur.com" height="300" width="300" class="img-responsive">

Okay, enough spinning. This post will provide basic explanations for the following JavaScript methods: `.forEach()`, `.map()`, `.filter()`, `.reduce()`, `.find()`, and `reduce`. 
----
## forEach
---
This method executes a callback once for each element in the array. The return value `undefined`, and this method changes original array.

```javascript
let ages = [23, 26, 30, 33, 36];

ages.forEach(function(age) {
  console.log(age + 10);
});

//expected output 33
//expected output 36
//expected ouptut 40
//expected outpot 43
//expected output 46

> undefined
```
## map
---
This method executes a callback function once for each element in an array, in order, and constructs a new array from the results. This method might be preferable if you want to change data in your array. Because `map` actually alters the data in the array, it tends to be more commonly used than `forEach`. 

```javascript
let ages = [23, 26, 30, 33, 36];

const agesInTenYears = peopleAges.map(age => age + 10)

console.log(agesInTenYears)

> [33, 36, 40, 43, 46]
```

## filter()
---
This method creates a new array with all elements that pass the test implemented by the provided function. This is a great method for extracting elements based on a condition.

```javascript
let ages = [23, 26, 30, 33, 36];

const overThirtyThree = age.filter(age => age >= 33)

console.log(overThirtyThree)

> [33, 36]
```
## find
---
This method eturns the value of the first element in the array that satisfies the provided testing function. Otherwise undefined is returned.

```javascript 
let ages = [23, 26, 30, 33, 36];

const earlyTwentiesAge = ages.find(function(age) {
	return age < 26;
});

console.log(youngestAge)

> 23
```
## reduce
---
This method applies a function against an accumulator and each element in the array (from left to right) to reduce it to a single value. This is great a method for when you want to find a cumulative value based on elements across the array. You can also concatenate values with `reduce()`.

```javascript
const prices = [25, 50, 10, 25, 500]

const addPrices = (accumulator, price) => accumulator + price;

console.log(prices.reduce(addPrices));

> 610
```

When you want to manipulate data structures, these methods will help. **But remember**, `foreach` doesn't return a new array, so it can't chain with other methods.
