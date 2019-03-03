---
layout: post
title:      "Filth Finder: Migrating to React Hooks"
date:       2019-3-02 00:08:15 -0500
permalink:  filth_finder_react_hooks
---

Have you ever walked around NYC looking for a place to eat? Have you ever wished there was a way to find the health inspections grades along with violations for restaurants near you at your finger tips? Well, now you can do that using [Filth Finder](https://filth-finder-nyc.herokuapp.com/), a React app with a Rails backend. This application fetches data from the [NYC Open Data API](https://data.cityofnewyork.us/Health/DOHMH-New-York-City-Restaurant-Inspection-Results/43nn-pn8j). I recently took the opportunity to rewrite one of the components using React Hooks! This post will provide a brief overview of the app and explain how I migrated to hooks.

<img src="https://i.imgur.com/PkqpWMk.png" title="source: imgur.com" width="200" class="img-responsive">

## overview

Filth Finder has 3 components:

* EstablishmentSelector - selects the NYC establishments closest to the user using the [react-geolocated library](https://www.npmjs.com/package/react-geolocated) via a rails API that uses Postgis and the [NYC Geo Client API](https://developer.cityofnewyork.us/api/geoclient-api) to geocode the establishments via a rake task. 
* EstablishmentList - lists all establishments and provides a link that the user can click to view health inspection details on the chosen establishment
* EstablishmentDetail - lists the date, grade, and violations for each time the establishment/restaurant received a health inspection

<img src="https://i.imgur.com/Ugyx0J6.png" title="source: imgur.com" height="500"/>


```jsx
import React from "react";
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
      <Box pad={{ horizontal: 'xsmall' }}>
        <Heading level={4}>
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