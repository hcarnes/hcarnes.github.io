---
layout: post
title:      "How Git Subtree Helped Me Deploy to Heroku"
date:       2018-08-14 15:12:15 -0500
permalink:  git_subtree
---

One thing to consider when you start developing an application is the organization of your git repos. Mono-repo is an organization structure where all of your code is one repo, and multi-repo involves organizing code across multiple repos. When I built Greenthumb Gardens, I used the mono-repo structure, and I nested the client in a folder inside of the Rails API. If you've ever deployed to Heroku, you knows that you need a repo for your application. Well, I had 2 applications I needed to deploy (frontend and backend), but I only had 1 git repo. This was a problem. With that said, this post will explain how I figured out how to deploy my app to Heroku, despite the fact that the client and server were in the same repo.

You can check (Greenthumb Gardens)[https://greenthumb-gardens.herokuapp.com/]. Unfortunately, due to changes in the Google Maps API, the map now has some restrictions. However, the garden search and plant adding feature still works.

# Changing from Sqlite3 to Postgres
In order to deploy the backend to Heroku, I had to change my database from Sqlite3 to Postgres. In the gemfile, I change ‘gem sqlite3’ to ‘gem pg’. Next, I updated the`config/database.yml` file. Instead of manually changing anything that referenced Sqlite3 to Postgres, I set up a new Rails app and installed Postgres as its database, so that I could simply copy the fresh `config/database.yml` over to my current project. I ran `rake db:migrate` to make sure my changes were updated.

# Using git subtree
Because my client app was in a folder nested inside of the backend app, I had to figure out a way to create a new repo within my main repo. That’s where `git subtree` comes in. `git subtree` allowed me to nest one repository inside another as a sub-directory.

First, I created a new Heroku repo for the client and pushed it the remote.

`heroku create greenthumb-gardens --remote heroku-frontend`

Next, I used `git subtree push` to push that repo to the remote instance of the Heroku application. I included `--prefix` to specify the path in the repo to the subtree I wanted to manipulate.

`git subtree push --prefix greenthumb-client/ heroku-frontend master`

Next, I created a new Heroku repo for the client and pushed it the remote.

`heroku create greenthumb-gardens-backend --remote heroku-backend`

Next, I pushed the repo.
`git push heroku-backend heroku:master`

# Getting ready for production
Now that the backend app is running using a separate repo, I set the new environment variable in the `greenthumb-client/.env.production` file.

`REACT_APP_BACKEND_HOST="https://greenthumb-gardens-backend.herokuapp.com"`

I also updated the Redux `action` file fetched made an async request to the backend application.

```javascript
export function addPlant(plantToAdd) {
  return async (dispatch) => {
    dispatch({ type: 'START_ADDING_PLANT' });
    const plantJSON = JSON.stringify({ plant: plantToAdd })
    const plantResponse = await fetch(`${process.env.REACT_APP_BACKEND_HOST}/plants`, {
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      },
      body: plantJSON,
      method: "post"
    })
    const plant = await plantResponse.json()
    dispatch({ type: 'ADD_PLANT', plant })
  };
}
```

# Next time
Next time I am creating a relatively simple full stack applicaiton, I would start out by creating different repositories for the frontend and backend and I would deploy them to Heroku at the beginning of the development process instead of waiting until the end.















