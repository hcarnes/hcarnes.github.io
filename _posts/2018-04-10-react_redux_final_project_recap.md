---
layout: post
title:      "React Redux Final Project Recap: Greenthumb Gardens"
date:       2018-04-10 15:12:15 -0500
permalink:  react_redux_final_project_recap
---

For my final project, I built an application with a React/Redux frontend and a Rails backend. I created [Greenthumb Gardens](https://github.com/hcarnes/greenthumb), which is an app that allows users to search community gardens in NYC and update and view the plants that can be found at each garden. I used the NYC open data API to get information on each garden.

## Container Components vs. Stateless Components

The project required the app to have 2 container components and 5 stateless components. This pushed me to understand the difference between each, as well as how they work together. For this post, I'll share 2 components (1 stateless and 1 container) and explain my reasoning behind how I categorized each component.

**Stateless functional components**

* get data exclusively from their props
* sometimes are called presentational components because they are concerned with how things look
* they pretty much just take props and return the markup of the component

Here is an example of a stateless component found in Greenthumb Gardens:

```jsx
import List from 'material-ui/List';
import React, { Component } from 'react';
import GardenListItem from './GardenListItem'
import { Link } from 'react-router-dom';

class GardenList extends Component {

  render() {
    return (
      <div>
        <List component="nav">
          {this.props.gardens.map((garden, index) => (
            <Link to={`/gardens/${index}`}>
              <GardenListItem garden={garden} />
            </Link>
            ))}
        </List>
      </div>
    )
  }
}

export default GardenList;
```
So you can see, `GardenList` renders each `garden` found in the array of `gardens` as a link and `gardens` is accessed through props. The main function here is to render gardens to the page.

**Container Components**

* manage stateless functional components
* pass data and callback methods down to their children as props
* provide data and behavior to other components


```jsx
import React, { Component } from 'react';
import TextField from 'material-ui/TextField';
import { connect } from 'react-redux';
import { searchGardens } from '../actions/searchGardens';
import { bindActionCreators } from 'redux';
import GardenList from '../components/GardenList'

class FilterableGardenList extends Component {

  handleFilter = (event) => {
    this.props.searchGardens(event.currentTarget.value)
  }

  componentDidMount() {
    this.props.searchGardens(this.props.query)
  }

  render() {
    return (
      <div>
        <TextField onChange={this.handleFilter} placeholder="Search" />
        <GardenList gardens={this.props.gardens} />
      </div>
    )
  }
}

const mapDispatchToProps = (dispatch) => {
  return bindActionCreators({ searchGardens: searchGardens }, dispatch)
}

const mapStateToProps = (state) => {
  return { gardens: state.gardens.filter(garden => garden.garden_name.toLowerCase().includes(state.query)) };
};

export default connect(mapStateToProps, mapDispatchToProps)(FilterableGardenList)
```
Container components are the integration point for Redux and the stateless functional components. `FilterableGardenList` uses Redux's `connect` to map the store's state and dispatch to the props of a component. You can also find a stateless component `GardenList` inside of this container component.

While container components and stateless components serve different purposes, there is a gray area in regards to where to draw the distinction. This [blog post](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) written by none other than Mr. Dan Abramov himself, was extremely helpful in terms of figuring out "What the heck is the difference between "stateless" and "container" components.

That's a wrap for my Flatiron project recaps because this is the last project for the program. And my love affair with programming has only begun!

<img src="https://i.imgur.com/yxWLcMz.gif" title="source: imgur.com" height="300" width="300" class="img-responsive">


