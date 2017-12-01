---
layout: post
title:      " Refactoring Ruby RegEx"
date:       2017-12-01 01:01:41 +0000
permalink:  refactoring_ruby_regex
---


Code that is unnecessarily long is not fun to read. It has code smell, a phrase coined in programming for code that is hard to read and error prone. Fortunately, refactoring can help eliminate even the toughest odors. 

Refactoring involves reevaluating and rewriting code to increase efficiency and readability. This adds an extra challenge, but it is well worth it to ensure the code not only works but can also be read and maintained by others in the future. To demonstrate refactoring, I'll show you how I started with a long method and refactored it to find a cleaner solution.

Both methods below accomplish the same task: accept an argument of a phone number and return true for valid phone numbers, regardless of format, and return false for invalid phone numbers. Examples of valid numbers include: 1234567890, 123-456-7890, (123)456-7890, 123 456 7890, and (123)4567890. 

**Code smell:**

![smell](https://i.imgur.com/B9JJC0F.png)

**Clean clode:**

![clean](https://i.imgur.com/MEJbA8U.png)

I initially approached the problem by creating a separate RegEx method for each different version of a valid phone number. I enclosed the method in extraneous parentheses and inserted unnecessary characters, such as  “^” and "$". Each method was separated by a pipe “|” to represent “or”. Here is the breakdown on the RegEx for each valid number.To begin refactoring  the method, I had to find commonalities in the valid phone
numbers.


|1234567890 | 123-456-7890  | (123)456-7890| 123 456 7890  | (123)4567890 |
| :--------: | :--------: | :--------: |:--------: | :--------: |
|   `/\d{10}` | `^\d{3}-\d{3}-\d{4}$`   | `^[(]\d{3}+[)]\d{3}-\d{4}`  | `^\d{3}.\d{3}.\d{4}` |  `^[(]\d{3}[)]\d{7}` |


All of the numbers include 10 digits. The first three digits are sometimes preceded by an open parentheses. The next three digits are also sometimes divided by a closed parentheses, a space, or a dash. Let’s go ahead and create a skeleton number with these separate digit groups. If you test the code below against our valid numbers in Rubular, only 1234567890 will match. 

`\d{3}\d{4}\d{5}`

In some of the valid numbers, the area code might be enclosed in parentheses. Let’s add an expression that allows a character to be present once or not at all “\?”. To be sure we are allowing a parentheses to be present once or not at all, let’s include “\(?” before and “\)?”  after the first three digits. If you test the code below against our valid numbers in Rubular, only 1234567890 and (123)4567890 will match. 

`\(?\d{3}\)?\d{3}\d{4}`

What about the numbers with either a space or a dash between groups of digits? In regex, a space can be represented by “\s”. If we want to check for a space or a dash or nothing we can use the following syntax: “(\s|-)?”. Let’s place this syntax between the digit groups. If you test the code below against our valid numbers in Rubular, all valid numbers will match.

`\(?\d{3}\)?(\s|-)?\d{3}(\s|-)?\d{4}`

Ah, much better.












