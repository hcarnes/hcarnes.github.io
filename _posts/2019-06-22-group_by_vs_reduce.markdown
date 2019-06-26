---
layout: post
title: "SQL GROUP BY using JavaScript"
date: 2019-6-25 00:01:15 -0500
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

Aggregate functions operate on the groups created by `GROUP BY` to help you answer interesting questions about your data. Below lists some of the aggregate functions: (*Source: [PostgreSQL Documentation](http://www.postgresqltutorial.com/postgresql-aggregate-functions/)*).

* AVG() – return the average value.
* COUNT() – return the number of values.
* MAX() – return the maximum value.
* MIN() – return the minimum value.
* SUM() – return the sum of all or distinct values.

Let's answer some questions about our data using `GROUP BY` and aggregate functions!

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
What if you're working in JavaScript and have an array of objects you need to group by a particular property? Well, let's extend the example above by assuming we have an array of staff location objects, and each object has the property of `name` and `city` and `state`. JavaScript's `reduce` method is one way to approach the problem.

> According to the the MDN documentation, "the reduce() method executes a reducer function (that you provide) on each element of the array, resulting in a single output value.

You can write a function that takes 2 parameters: the array of objects and the properties that you would like to group the object by. The properties will represent the "bucket" that you put your `staffLocations` in based on their state. 


The reduce method below takes the following arguments:
 
* **accumulator** - This stores the return values created each time the callback function is invoked. This is returned when the method is complete (assuming the array passed in is not empty, in which case the initial value is returned).
* **object** - This is the current object being manipulated in the array.
* **callback** - This is the function you want to execute on each object in the array.
* **initialValue** - The first time the reducer function runs, this will be the accumulator value. Below, the `initialValue` is `{}`. 

```javascript
const staffLocations = [
  { name: "Hannah", city: 'Houston', state: 'GA' },
  { name: "Ilhan",  city: 'Atlanta', state: 'GA' },
  { name: "Preet",  city: 'Houston', state: 'TX' },
  { name: "Adam",  city: 'Austin', state: 'TX' },
  { name: "Preston", city: 'New York City', state: 'NY' },
  { name: "Anna", city: 'Houston', state: 'TX' },
  { name: "Jakub",  city: 'Atlanta', state: 'GA' },
];

const groupBy = (objectArray, ...properties) => {
  return [...Object.values(objectArray.reduce((accumulator, object) => {
    const key = JSON.stringify(properties.flatMap((x) => object[x] || null));

    if (!accumulator[key]) {
      accumulator[key] = [];
    }
    accumulator[key].push(object);
    return accumulator;
  }, {}))];
}

const groupedStaffLocations = groupBy(staffLocations, 'state');

groupedStaffLocations
```

`groupedStaffLocations` looks like:

```javascript
[
  [
    { name: "Preet", city: "Houston", state: "TX" },
    { name: "Adam", city: "Austin", state: "TX" },
    { name: "Anna", city: "Houston", state: "TX" },
  ],
  [
    { name: "Hannah", city: "Houston", state: "GA" },
    { name: "Ilhan", city: "Atlanta", state: "GA" },
    { name: "Jakub", city: "Atlanta", state: "GA" },
  ],
  [
    { name: "Preston", city: "New York City", state: "NY" },
  ]
]
```

The callback steps include the following:

* Read the values of the grouping properties, and store them in `key`. This symbolizes the group
* If the accumulator doesn't have an existing group for the values in `key`, create a new group
* Put the object in the group

*Source: [MDN: Reduce: Grouping objects by property](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce#Grouping_objects_by_a_property)*

# Getting insights from the data
After you reduce data to buckets of information with key value stores, you can map the same data to answer interesting questions, like the question we answered above: *"Which state has the most cities?"*.


```javascript
const groupedCities = groupBy(cities, 'state');

// sort by length of array
let sortedArr = groupedCities.sort((a, b) => b.length - a.length);
// get the state of the first array, which would have the greatest length
sortedArr[0][0]['state'];

// returns:
// "TX"
```

# Multiple properties

This function also supports grouping by multiple properties, so it works like `GROUP BY` in SQL:

```javascript
const cityGroupedStaffLocations = groupBy(staffLocations, 'state', 'city');
```

In this case, `cityGroupedStaffLocations` returns groups representing staff that live in the same city:

```javascript
[
  [
    { name: 'Hannah', city: 'Houston', state: 'GA' },
  ],
  [
    { name: 'Ilhan', city: 'Atlanta', state: 'GA' },
    { name: 'Jakub', city: 'Atlanta', state: 'GA' },
  ],
  [
    { name: 'Preet', city: 'Houston', state: 'TX' },
    { name: 'Anna', city: 'Houston', state: 'TX' },
  ],
  [
    { name: 'Adam', city: 'Austin', state: 'TX' },
  ],
  [
    { name: 'Preston', city: 'New York City', state: 'NY' },
  ]
]
```

This can be easily paired up with `map` to get the number of staff in each city:
```js
cityGroupedStaffLocations.map(cityStaff => ({location: `${cityStaff[0].city}, ${cityStaff[0].state}`, numberOfStaff: cityStaff.length}))
```
returning:
```js
[
  { location: 'Houston, GA', numberOfStaff: 1 },
  { location: 'Atlanta, GA', numberOfStaff: 2 },
  { location: 'Houston, TX', numberOfStaff: 2 },
  { location: 'Austin, TX', numberOfStaff: 1 },
  { location: 'New York City, NY', numberOfStaff: 1 },
]
```
# JSON.stringify???

```js
const key = JSON.stringify(properties.flatMap((x) => object[x] || null));
```

When reading through the `groupBy` method, did you notice that the `key` was JSON? In order to ensure that multiple grouping properties can be passed into the function (`state`, `name`, or `city`), `key` had to be an array of the corresponding values. In JavaScript, `Object`s can only use strings and symbols as keys. Transforming the group (`key`) to JSON allows us to cheat JavaScript's lack of deep structural equality by using simple JSON string comparison. When the values in the group convert to the same JSON, they'll be considered part of the same group. While this probably hurts performance, it's the most succinct way I've found with vanilla JavaScript to use arrays as keys.

 <div class="friends">
  <a href="/img/cool.svg">
    <img src="/img/cool.svg" alt="a cat saying 'cool'" width="500px"/>
  </a>
</div>