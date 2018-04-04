---
layout: post
title:      "Scope and Hoisting"
date:       2018-04-03 09:09:29 -0400
permalink:  scope_and_hoisting
---
## Scope: var, let, const
This post will discuss the difference between `var`, `let`, and `const` in terms of scope and hoisting in JavaScript. Once you understand the pros and cons of each, it makes it easier to interpret errors and avoid bugs. 

>Scope is the current context of execution. The context in which values and expressions are "visible," or can be referenced. If a variable or other expression is not "in the current scope," then it is unavailable for use. 

### Function Scope

If `var` is defined inside of a function, it is function scoped. `var` is globally scoped when defined outside of the function. If a variable defined with `var` is defined inside of a function and called outside of that function, it will return a `ReferenceError`. 

The example below shows that when `scrapedIce` is called, you can log out `raspa`, which is defined inside of that function. If you `console.log(raspa)` outside of the function, you will see `global snow cone variable` because you have access to the global `var snowcone` defined outside of the function. Finally, you will receive a `ReferenceError` if you try to access the `syrup` variable outside of the `scrapedIce()` function.

```javascript
var raspa = "the global snow cone variable";  

function scrapedIce(){  
   var raspa = "the local snow cone variable";
   var syrup = "sweet and tasty"  
   console.log(raspa)
}  
scrapedIce(); // expected output: a local snowcone variable
console.log(raspa) // expected output: a global snowcone variable
syrup // expected output: Uncaught reference error: syrup is not defined
```

The variable defined in the local scope [shadows](https://en.wikipedia.org/wiki/Variable_shadowing) the variable in the global scope. It is created and destroyed every time the function is executed, and it cannot be accessed by any code outside the function.

`var` determines the variable's scope, so what happens when `var` is removed?

```javascript
raspa = "the global snowcone variable";  

function scrapedIce(){  
   raspa = "the local snowcone variable";
   syrup = "sweet and tasty";  
   console.log(raspa)
}  
scrapedIce(); // expected output: a local snowcone variable
console.log(raspa) // expected output: a local snowcone variable
syrup // expected output: "sweet and tasty"
```

You can access `syrup` outside of the function because it's not bound to that scope, since it's a global variable. This is almost always a bad idea, and in general you should never use global variables in JavaScript, unless you really intend for something to be accessible (and mutatable) globally.

### Block Scope

Variable definitions prefixed with `let` and `const` will be block/lexically scoped (a block is the code enclosed in curly brackets `{}`), in contrast to variables defined with `var`, which are functionally scoped. This property is handy because it limits variable scope to the block it is used in, which can prevent errors if you're editing a big function where there could accidentally be variables with the same name (in general, this should be avoided, but we'll save that for a later post).

```javascript
let raspa = "snow cone outside of the block";
{
  let raspa = "snow cone inside of the block";
}
console.log(raspa); // logs "snow cone outside of the block;
```
And we can see we get the same output if we use `const`. 

```javascript
const raspa = "snow cone outside of the block";
{
  const raspa = "snow cone inside of the block";
}
console.log(raspa); // logs "snow cone outside of the block;
``` 

Okay, let that sink in while you enjoy a refreshing raspa.

<img src="https://i.imgur.com/L8hzyFt.gif" alt="Refreshing raspa beverage" height="300" width="300" class="img-responsive">

## Hoisting: var, let, const

In JavaScript, variable and function declarations are stored into memory during the compile phase before any code is actually executed. This is referred to as **hoisting** because it seems like the variables and function delarations are magically "hoisted" to the top of the scope.

```javascript
console.log(snowball);
var snowball = "what snow cones are called in New Orleans"
```
This is in contrast to logging variables that have not been defined at all:

```javascript
console.log(foobarbaz) // Uncaught ReferenceError: foobarbaz is not defined
```

Because the compiler sees that somewhere in the scope, the `snowball` variable is being defined, it moves the definition to the top of the scope, preventing an error from being generated. This can cause subtle bugs in your applications if you're using `var`, which initializes the variable when hoisting to `undefined`:

```javascript
var price = 2;

if (price > 3) {
  var consumerOpinion = "Snow balls are $5 - that's too expensive!"; 
}
console.log(consumerOpinion)
// undefined
```

Since `consumerOpinion` is hoisted to the top of the scope, the application continues executing instead of stopping due to a `ReferenceError`, which would be preferable, since the branch that actually sets the variable to a sensible value can never be executed as-defined. For this reason, it makes to use `let` to define your variable because it is not initialized to `undefined` like `var`, so it will generate a `ReferenceError`:

```javascript
let price = 2;

if (price > 3) {
  let consumerOpinion = "Snow balls are $5 - that's too expensive!"; 
}
console.log(consumerOpinion)
// Uncaught ReferenceError: consumerOpinion is not defined
```

This generates an error, as expected, because the branch that sets the value of `consumerOpinion` was never visited. Instead of silently continuing execution, the program errors out, sending an immediate signal to the developer that something went wrong which makes this issue much easier to investigate.

## Const

As we learned earlier, `const` and `let` are block scoped. Unlike `let`, `const` cannot be reassigned. The examples below try to redeclare the snowball variable but to no avail.

```javascript
const snowball = 'snowball';
snowball = 'raspa'; // Uncaught TypeError: Assignment to constant variable.
```

The next one doesn't work either.

```javascript
const snowball = "snowball";
const snowball = "raspa"; //Uncaught SyntaxError: Identifier 'snowball' has already been declared
```

Variables defined by `const` are also hoisted but they are not initialized, like `let`. In general you should use `const` to accomplish your work whenever possible.

## BONUS: Temporal Dead Zone

While it may appear that `const` and `let` declarations are not hoisted to the top of the scope like `var` is, that is not the case. The only difference is that `const` and `let` do not assign `undefined` to the freshly-hoisted variable declaration, which gives them the favorable property of generating `ReferenceError`s when accessed unexpectedly. So what does it mean to be hoisted but unititialized?

```javascript
const snowballStand = function() {
  const snowballMaker = function() {
    console.log(snowballType)
  }
  const snowballType = "Raspberry Raspa"
  snowballMaker()
  // log output: "Raspberry Raspa"
}
```

You might expect this code to generate a `ReferenceError` since `snowballType` hasn't been set when it appears in the `snowballMaker` function. However, that's not how JavaScript works. Because `const` and `let` are both hoisted to the top of their scope (block scope), when the `snowballType` logging code is evaluated, no error is generated. Also, an error is not generated when calling the `snowballMaker` function, because in the line above we actually assign a value to this variable. If we tried to execute the function before we assigned a value to `snowballType`, we would receive a `ReferenceError` as expected. The time between the hoisted definition and the assignment to that definition is known as the `temporal dead zone`. You can learn more about why it exists [here](http://2ality.com/2015/10/why-tdz.html)

>If you are interested in an even deeper dive on this topic, these articles might be useful: [Dev.to](https://dev.to/sarah_chima/var-let-and-const--whats-the-difference-69e),
[codeburst](https://codeburst.io/part-2-var-vs-const-vs-let-69ea73fe76c1)



	
	
	
	
	
	
	
	
	
	
	
