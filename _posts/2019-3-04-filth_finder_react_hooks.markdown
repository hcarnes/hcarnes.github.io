---
layout: post
title:      "Filth Finder: Rewriting to Use React Hooks"
date:       2019-3-03 00:08:15 -0500
permalink:  filth_finder_react_hooks
---

Have you ever walked around NYC looking for a place to eat? Have you ever wished there was a way to find the health inspection info for restaurants near you? Well, now you can do that using [Filth Finder](https://filth-finder-nyc.herokuapp.com/), a React app with a Rails backend. This application fetches data from the [NYC Open Data API](https://data.cityofnewyork.us/Health/DOHMH-New-York-City-Restaurant-Inspection-Results/43nn-pn8j). I recently took the opportunity to rewrite one of the components using React Hooks! This post will provide a brief overview of the app and explain how I migrated to [hooks](https://reactjs.org/docs/hooks-intro.html).

<a href="https://imgur.com/0hp4qWf"><img src="https://i.imgur.com/0hp4qWf.gif" title="source: imgur.com" /></a>

## App overview

Filth Finder has 3 components:

* `EstablishmentSelector` - displays the NYC establishments using the [react-geolocated library](https://www.npmjs.com/package/react-geolocated) via a rails API that uses Postgis and the [NYC Geo Client API](https://developer.cityofnewyork.us/api/geoclient-api) to geocode the establishments
* `EstablishmentList` - lists all establishments and provides a link to the EstablishmentDetail component
* `EstablishmentDetail` - lists the date, grade, and violations for each time the establishment/restaurant received a health inspection

I also used [Grommet](https://github.com/grommet/grommet), a react-based framework for styling.

## Getting the data through the Establishment model

The `EstablishmentDetail` component references the `Establishment` model, which gets the data we want to display to the user. The code below starts with `fetchdetails` method that makes an async request to the NYC Open Data API and returns the details about a chosen establishment by using a restaurant's unique camis id as a parameter. The details are returned as json. The Establishment model has a static `detail` method that makes an async request by calling the `fetchDetails` method that returns a list of health inspections, including the grade, date, and health inspection violations found ordered by date.

```jsx
import axios from "axios";

const fetchDetails = async camis => {
  const response = await axios.get(
    "https://data.cityofnewyork.us/resource/9w7m-hzhe.json",
    { params: { camis } }
  );
  return response.data;
};
class Establishment {
  static async detail(camis) {
    const detailsData = await fetchDetails(camis);
    const aggViolations = violations => {
      return violations.flatMap(violation => {
        if (violation.violation_code && violation.violation_description) {
          return [{
            code: violation.violation_code,
            description: violation.violation_description
          }];
        } else {
          return [];
        }
      });
    };
    const aggInspections = details => {
      const violationDateBuckets = details.reduce((objectsByKeyValue, obj) => {
        const value = obj["inspection_date"];
        objectsByKeyValue[value] = (objectsByKeyValue[value] || []).concat(obj);
        return objectsByKeyValue;
      }, {});
      return Object.values(violationDateBuckets).map(
        violationsByInspectionDate => {
          return {
            grade: violationsByInspectionDate[0].grade,
            date: violationsByInspectionDate[0].inspection_date,
            violations: aggViolations(violationsByInspectionDate)
          };
        }
      );
    };

    const establishmentDetail = {
      dba: detailsData[0].dba,
      inspections: aggInspections(detailsData).sort(
        (a, b) => Date.parse(b.date) - Date.parse(a.date)
      )
    };

    if (establishmentDetail) {
      return establishmentDetail;
    } else {
      return null;
    }
  }
}

export default Establishment;
```

## Before hooks

Now, that we know how more about the application, as well as how we get the data for our stateful `EstablishmentDetail` component, let's see how the code looked before hooks. Below, we have a class component where the `camis` is passed in as a prop. The state is initially set in the constructor. The `camis` is set by accessing props and `establishments` is set to `null`. Next, we use the `componentDidMount` callback, which is called immediately after the componenet is rendered, to make an async request to the NYC Open data API. We also use the `setState` callback to ensure that our fresh establishment shows up for the user when the page rerenders.

```jsx
import React from "react";
import Establishment from "../models/Establishment";
import { Heading, Text, Accordion, AccordionPanel, Box } from "grommet";

class EstablishmentDetail extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      camis: props.match.params.camis,
      establishments: null
    };
  };

  componentDidMount = async () => {
    const establishment = await Establishment.detail(this.state.camis);
    this.setState({establishment});
  };

  const AccordionLabel = ({date, grade}) => {
    return (
      <Box>
        <Heading>
          <span>{`${new Date(date).toLocaleDateString()}`}</span> -
          <span> {grade}</span>
        </Heading>
      </Box>
    )
  }

  if (establishment) {
    return (
      <>
        <Box align="center">
          <Heading color="brand">Violations at {establishment.dba}</Heading>
          <Text>CAMIS: {camis}</Text>
          <ul className={styles.EstablishmentDetail}>
            <Accordion>
              {establishment.inspections.map(inspection => {
                return (
                  <AccordionPanel
                    label={<AccordionLabel date={inspection.date} grade={inspection.grade} />}
                  >
                    <li key={inspection.date}>
                      <Box>
                        <ul>
                          {inspection.violations.map(v => {
                            return (
                              <li key={inspection.date + v.code}>
                                <p>
                                  {v.code} - {v.description}
                                </p>
                              </li>
                            );
                          })}
                        </ul>
                      </Box>
                    </li>
                  </AccordionPanel>
                );
              })}
            </Accordion>
          </ul>
        </Box>
      </>
    );
  } else {
    return <div>Loading violations for CAMIS: {camis}</div>;
  }
};

export default EstablishmentDetail;

```
## Adding hooks...finally!

I had to upgrade my React version to 16.8, so that I could use the new React hooks API. Next, I had to import `useState` and `useEffect`. Then I was ready to remove the class syntax, along with the constructor and begin setting the state using the `useState` function. We don't need to use the class syntax used in the previous approach because hooks allow you to access React state and lifecycle features from function components.

According to the React documentation, `useState` uses array destructuring to return 2 things: 1) the current state value and 2) a function that lets you update it. In the code below, we see that `establishment` is the current state value and `setEstablishment` is the function that let's you update the state. The only argument `useState` takes is the initial state, which in our case is `null` because we must wait to fetch data from the API to set our state.

According to the React documentation, `useEffect`, serves the same purpose as `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` in React classes. In our case, our component fetches data after the component is already rendered. React runs the effects after every render, so you can see below that we fetch our data by calling `fetchEstablishment(camis)` after every render by using the `useEffect` hook. The second argument passed into to the `useEffect` function ([camis]) ensures that if there are no changes that the state doesn't change. This helps with performance. Note, the `camis` is still accessed through the props, but here it is set to its own variable.

```jsx
import React, {useState, useEffect} from "react";
import Establishment from "../models/Establishment";
import { Heading, Text, Accordion, AccordionPanel, Box } from "grommet";

const EstablishmentDetail = (props) => {
  const camis = props.match.params.camis;
  const [establishment, setEstablishment] = useState(null);

  const fetchEstablishment = async (camis) => {
    const fetchedEstablishment = await Establishment.detail(camis);
    setEstablishment(fetchedEstablishment);
  }

  useEffect(() => {
    fetchEstablishment(camis)
  }, [camis]);

  const AccordionLabel = ({date, grade}) => {
    return (
      <Box>
        <Heading>
          <span>{`${new Date(date).toLocaleDateString()}`}</span> -
          <span> {grade}</span>
        </Heading>
      </Box>
    )
  }

  if (establishment) {
    return (
      <>
        <Box align="center">
          <Heading color="brand">Violations at {establishment.dba}</Heading>
          <Text>CAMIS: {camis}</Text>
          <ul className={styles.EstablishmentDetail}>
            <Accordion>
              {establishment.inspections.map(inspection => {
                return (
                  <AccordionPanel
                    label={<AccordionLabel date={inspection.date} grade={inspection.grade} />}
                  >
                    <li key={inspection.date}>
                      <Box>
                        <ul>
                          {inspection.violations.map(v => {
                            return (
                              <li key={inspection.date + v.code}>
                                <p>
                                  {v.code} - {v.description}
                                </p>
                              </li>
                            );
                          })}
                        </ul>
                      </Box>
                    </li>
                  </AccordionPanel>
                );
              })}
            </Accordion>
          </ul>
        </Box>
      </>
    );
  } else {
    return <div>Loading violations for CAMIS: {camis}</div>;
  }
};

export default EstablishmentDetail;
```
## In Summary

Hooks are JavaScript functions that allow you access state and lifecycle features. This means that function components can now handle state all on their own. That's pretty interesting to me because many bloggers often refer to function components as stateless components, and now that's no longer an accurate way to categorize type of components.

`useState` lets you add state to function components
`useEffect` lets you handle other changes that may affect the state, such as data fetching


