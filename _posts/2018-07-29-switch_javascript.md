---
layout: post
title:      "JavaScript's Switch to the Rescue"
date:       2018-07-28 15:12:15 -0500
permalink:  javascript-switch
---

Conditional statements (`if...else`) execute code if a condition is true. If the condition is false, another statement is executed. In JavaScript, you can also use a `switch` statement to evaluate multiple conditions against the same expression. This post will explain when you should use a `switch` and how it works.

The code below generates a random number and executes a statement based on 2 conditions: either the number is greater than 500 or it's not. It's easy to read the code and figure out what's going on, and it's an efficient way to get the desired output.

```javascript
const getNumberRange = () => {
  const number = Math.round(Math.random() * 100);

  if (number > 500) {
    console.log(`${number} is more than 500.`);
  } else {
    console.log(`Wow!! Your number is only ${number}.`);
  }
}

// getNumberRange()
// Wow!! Your number is only 66.
```

## When you should switch to switch  
Things get smelly once when we start adding more than 2 conditions for each number that is evaluated. The code below takes a random number, tests it against 7 possible ranges, and executes some code when the statement is true.

```javascript
const getNumberRange = () => {
  const number = Math.round(Math.random() * 100);

  if (number > 500) {
    console.log(`${number} is more than 500`);
  } else if (number > 250) {
    console.log(`${number} is more than 250 and less than 500.`);
  } else if (number > 125) {
    console.log(`${number} is more than 125 and less than 250.`);
  } else if (number > 75) {
    console.log(`${number} is more than 75 and less than 125.`);
  } else if (number > 37.5) {
    console.log(`${number} is more than 37.5 and less than 75.`);
  } else if (number > 18.75) {
    console.log(`${number} is more than 18.75 and less than 37.`);
  } else {
    console.log(`Wow!! Your number is only ${number}.`);
  }
}

// getNumberRange()
// 31 is more than 18.75 and less than 37.
```
It has more conditions, so it takes longer to run. If you have a variable that you are testing against multiple conditions and find yourself repeating `else if` over and over, it is probably a good time to explore using a `switch` statement because it's more readable and you will get better performance.

## Switch to the rescue!
If you understand how the `switch` statement is different than regular conditional statements, you can understand why it performs better. Every `switch` statement has an expression to be evaluated. In the example below, `true` is being evaluated. Each case is tested against the expression, and if there is a match, the code will execute and the `break` keyword will signal the code to stop. In most cases, this is faster than the lengthy process of running through all possible conditions (ahem, `if...else`).

<img src="https://i.imgur.com/WFtRmyf.png" alt="rescue_cat" width="400px"/>


You can also add a default case that will run if there aren't any case matches. To that end, it's important to put the most probable condition first, so that the code will exit as soon as possible. In the code below, the widest range (`number > 500`) is is checked first all the way down the most specific range (`number > 18.75`), which is right before the default option.

```javascript
const getNumberRange = () => {
    const number = Math.round(Math.random() * 100);

    switch (true) {
         case number > 500:
             console.log(`${number} is more than 500`);
             break;
         case number > 250:
             console.log(`${number} is more than 250 and less than 500.`);
             break;
         case number > 125:
             console.log(`${number} is more than 125 and less than 250.`);
             break;
         case number > 75:
             console.log(`${number} is more than 75 and less than 125.`);
             break;
         case number > 37.5:
             console.log(`${number} is more than 37.5 and less than 75.`);
             break;
         case number > 18.75:
             console.log(`${number} is more than 18.75 and less than 37.`);
             break;
         default:
             console.log(`Wow!! Your number is only ${number}`);
     }
}

// getNumberRange()
// 95 is more than 75 and less than 125.
```

There are others ways to evaluate expressions using `switch`. This [Digital Ocean post](https://www.digitalocean.com/community/tutorials/how-to-use-the-switch-statement-in-javascript#switch) covers simple statements, ranges (like the example discussed here), as well as when you have multiple cases to consider. Now, next time you find yourself writing multiple `if...else` statements that evaluate the same expression, I hope a lightbulb 💡 goes off in your brain that tells you to consider the `switch` statement.

## BONUS: Snippets

If you ever want to test your code using Chrome DevTools, you can create a script called a [Snippet](https://developers.google.com/web/tools/chrome-devtools/snippets). Navigate to the Sources tab, create and save a Snippet, and then run the script. After that, you can call the method in the console to see your output. I used Snippets to test the code in this post.

<img src="https://i.imgur.com/xw4hiqn.png" alt="Snippet"/>









