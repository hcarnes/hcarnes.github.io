---
layout: post
title:      "JavaScript for loop: a closer look"
date:       2019-2-17 00:08:15 -0500
permalink:  javascript_for_loop
---

When I started learning JavaScript, ES8 was already released, and I was initially pretty confused about the difference between the older style versus the ES6+ style. Once I started picking up the language, it became easier to recognize and appreciate how ES6 made many aspects of JavaScript not only more readable, but also easier to maintain. For this post, let's take a closer look at the traditional versus the modern `for loop`.

<img src="https://i.imgur.com/PkqpWMk.png" title="source: imgur.com" width="200" class="img-responsive">

## the old for loop
The old `for loop` was not exactly easy on the eyes. We want the code to take each element in the array and log it out into the console. But, there is a lot of setup: we have to initialize the index variable, ensure the loop will exit once it reaches the upper bound limit (the lenght of the array), and then make sure that the index variable increases on each iteration. Not only that, but we must access the variable using the index. The index with the traditional `for loop` is mutable, which means the value could be changed, which increases the likelihood of errors.

```javascript
const colors = ['red', 'blue', 'pink', 'green']

for (let index = 0; index < colors.length; index++) {
  console.log(colors[index])
}

// expected output
// red
// blue
// pink
// green
```
After considering all the steps involved in the traditional `for loop`, it's easy to understand why the more modern `for loop` is a breath of fresh air. The code below is more readable and doesn't require the programmer to use the index variable to set an upper bound nor does it use the index variable to access the desired element. The modern `for...loop` is block scoped and the variable color is a constant.

## the modern for loop
```javascript
const colors = ['red', 'blue', 'pink', 'green']

for (const color of colors) {
  console.log(color)
}

// expected output
// red
// blue
// pink
// green
```

It's pretty easy to jump on the modern `for loop` bandwagon simply because it's more readable and maintainable. Another great thing about the modern `for loop` is that the index variable is immutable, which decreases the chances for errors. 

One thing you may have noticed, however, it's not so obvious how you would access the index with the modern `for loop` because the code automatically ensures we operate on the desire element without the index being involved at all. One solution to this problem is to implement the `entries()` function of `Array`. This handy function returns an iterator, which has a key and a value. If you iterate over the iterator returned by `entries()`, you can access both the index and the value of that index in the original `colors` array. 

```javascript
const colors = ['red', 'blue', 'pink', 'green']

for (const entry of colors.entries()) {
  console.log(entry)
}

//expect output
// [0, "red"]
// [1, "blue"]
// [2, "pink"]
// [3, "green"]
```

Another way to access the index of the elements in an array would be to use destructuring. With this approach, we specify the index variable and the color and iterate over the iterator return by `colors.entries()`. Each time the we iterate on `colors.entries()`, the index and the value are destructured and placed into the variables set in `const [index, color]`.

```javascript
const colors = ['red', 'blue', 'pink', 'green']

for (const [index, color] of colors.entries()) {
  console.log(index + ': ' + color)
}

//expect output
// [0: red]
// [1: blue]
// [2: pink]
// [3: green]
```

As previously mentioned, the `entries()` method returns an iterator. The means that, well, we can iterate on the results of `entries()`. It also means that `entries()` implements the [Symbol.iterator](). More on this in the next post.

