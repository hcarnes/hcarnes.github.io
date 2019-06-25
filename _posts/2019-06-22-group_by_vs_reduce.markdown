---
layout: post
title:      "SQL's Group By & JavaScript's Reduce"
date:       2019-6-24 00:01:15 -0500
permalink:  group_by_reduce
---


This post explores how PostreSQL's `GROUP BY` clause and JavaScript's `reduce` method both have the ability to help you group objects/rows of data based on properties. I'm assuming you are already familiar with both `GROUP BY` and `reduce`, and that you are also (mildly) interested in gaining a deeper understanding of each.

# PostgreSQL's GROUP BY clause

> According the PostgreSQL documentation, "the GROUP BY clause is used to group together those rows in a table that have the same values in all the columns listed...The effect is to combine each set of rows having common values into one group row that represents all rows in the group."

Here is a refresher on what that this looks like in PostreSQL (*Source: [PostgreSQL tutorial](http://www.postgresqltutorial.com/postgresql-group-by/)*):

```SQL
SELECT column_1, aggregate_function(column_2)
FROM tbl_name
GROUP BY column_1;
```
Let's say we have a `cities` table that includes a list of cities with the properties of `name` and `state`, and we want to aggregate those cities by `state`.

<table>
  <caption><i>A list of United States cities and their states.</i></caption>
  <colgroup>
    <col>
    <col>
  </colgroup>
  <tr>
    <th scope="col">Name</th>
    <th scope="col">State</th>
  </tr>
  <tr>
    <td scope="row">San Antonio</td>
    <td>TX</td>
  </tr>
  <tr>
    <td scope="row">Atlanta</td>
    <td>GA</td>
  </tr>
  <tr>
    <td scope="row">Austin</td>
    <td>TX</td>
  </tr>
  <tr>
    <td scope="row">New York City</td>
    <td>NY</td>
  </tr>
  <tr>
    <td scope="row">Houston</td>
    <td>TX</td>
  </tr>
</table>

The following query will group together those rows in the table that have the same value in the `state` property.

```SQL
SELECT state
FROM cities
GROUP BY state;
```
Below is a visual representation of the result of the query. You can see that `GROUP BY` in this case acts as `SELECT DISTINCT`. In other words, `GROUP BY` removed all the duplicate states in our table.

<table>
  <caption><i>A list of all unique states in the cities table.</i></caption>
  <colgroup>
    <col>
  </colgroup>
  <tr>
    <th scope="col">State</th>
  </tr>
  <tr>
    <td style="background-color: lightblue">GA</td>
  </tr>
  <tr>
    <td style="background-color: yellow">NY</td>
  </tr>
  <tr>
    <td style="background-color: pink">TX</td>
  </tr>
</table>

# Getting insights from the data

The `GROUP BY` clause sets you up to answer questions about your data. Once you have have the data in buckets/group, you can determine things like "How many cities are in each state?" and "Which state has the most cities".

Below lists some of the aggregate functions that you can use to answer these questions (*Source: [PostgreSQL Documentation](http://www.postgresqltutorial.com/postgresql-aggregate-functions/)*).

* AVG() – return the average value.
* COUNT() – return the number of values.
* MAX() – return the maximum value.
* MIN() – return the minimum value.
* SUM() – return the sum of all or distinct values.

Let's answer some questions using PostgreSQL queries!

*How many cities are in each state?*

```SQL
SELECT state, COUNT(state)
FROM cities
GROUP BY state;
```

<table>
  <caption><i>Each state along with the number of cities in that state.</i></caption>
  <colgroup>
    <col>
    <col>
  </colgroup>
  <tr>
    <th scope="col">State</th>
    <th scope="col">Count</th>
  </tr>
  <tr>
    <td style="background-color: lightblue">GA</td>
    <td style="background-color: lightblue">1</td>
  </tr>
  <tr>
    <td style="background-color: yellow">NY</td>
    <td style="background-color: yellow">1</td>
  </tr>
  <tr>
    <td style="background-color: pink">TX</td>
    <td style="background-color: pink">3</td>
  </tr>
</table>

*Which state has the most cities?*

```SQL
SELECT state, COUNT(state)
FROM cities
GROUP BY state
ORDER BY count DESC
LIMIT 1
```

<table>
  <caption><i>The state with the most cities.</i></caption>
  <colgroup>
    <col>
    <col>
  </colgroup>
  <tr>
    <th scope="col">State</th>
    <th scope="col">Count</th>
  </tr>
  <tr>
    <td style="background-color: pink">TX</td>
    <td style="background-color: pink">3</td>
  </tr>
</table>

# JavaScript's reduce method
What if you're working in JavaScript and have an array of objects you need to group by a particular property? Well, let's extend the example above by assuming we have an array of city objects, and each object has the property of `name` and `state`. JavaScript's `reduce` method is one way to approach the problem.

> According to the the MDN documentation, "the reduce() method executes a reducer function (that you provide) on each element of the array, resulting in a single output value.

You can write a function that takes 2 parameters: the array of objects and the property that you would like to group the object by. The property will represent the "bucket" that you put your cities in based on their state. 


The reduce method below takes the following arguments:
 
* **accumulator** - This stores the return values created each time the callback function is invoked. This is returned when the method is complete (assuming the array passed in is not empty, in which case the initial value is returned).
* **object** - This is the current object being manipulated in the array.
* **callback** - This is the function you want to execute on each object in the array.
* **initial value** -  This is the value returned in the case the array passed in is empty. If the array is empty and doesn't have an initial value, you will get a type error.

```javascript
const cities = [
  { name: 'San Antonio', state: 'TX' },
  { name: 'Atlanta', state: 'GA' },
  { name: 'Austin', state: 'TX' },
  { name: 'New York City', state: 'NY' },
  { name: 'Houston', state: 'TX' },
];

const groupBy = (objectArray, property) => {
  return objectArray.reduce((accumulator, object) => {
    const key = object[property];
    if (!accumulator[key]) {
      accumulator[key] = [];
    }
    accumulator[key].push(object);
    return accumulator;
  }, {});
}

const groupedCities = groupBy(cities, 'state');

// groupedCities is:
// { 
//   TX: [
//     { name: 'San Antonio', state: TX }, 
//     { name: 'Austin', state: 'TX' },
//     { name: 'Houston', state: 'TX' }
//   ], 
//   NY: [{ name: 'New York City', state: 'NY' }] ,
//   GA: [{ name: 'Atlanta', state: 'GA' }] 
// }
```

The callback steps include the following:

* Determine the city object's state property, and store that in the `key` variable.
* If the accumulator doesn't have a matching `key` value/bucket for the current city, create a new bucket
* Put the city object in a bucket/key value based on its state

*Source: [MDN: Reduce: Grouping objects by property](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce#Grouping_objects_by_a_property)*

# Getting insights from the data
After you reduce data to buckets of information with key value stores, you can map the same data to answer interesting questions, like the question we answered above: *"Which state has the most cities?"*.


```javascript
const groupedCities = groupBy(cities, 'state');

// create an array of array with the key values
let arr = Object.values(groupedCities)
// sort by length of array
let sortedArr = arr.sort((a, b) => b.length - a.length);
// get the state of the first array, which would have the greatest length
sortedArr[0][0]['state'];

// returns:
// "TX"
```

The steps above mirror the SQL used to answer the same question. Both `GROUP BY` and `reduce` can set up you up to answer questions about your data. And that's pretty cool.

<div class="friends">
  <a href="/img/cool.svg">
    <img src="/img/cool.svg" alt="a cat saying 'cool" width="500px"/>
  </a>
</div>