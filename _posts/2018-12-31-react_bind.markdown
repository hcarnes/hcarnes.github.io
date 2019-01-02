---
layout: post
title:      "Binding the index in React"
date:       2019-1-01 00:08:15 -0500
permalink:  react_bind
---
I haven't been blogging lately because I've been solving programming problems for work now, and I'm not yet sure how to translate those to blog posts. With some time off for the holidays, I was fortunate enough to dive back into React. While I don't use this particular library for work, I find building anything with JavaScript improves my overall frontend toolbox.

I was working on an assignment from a React class that I started on Udemy a couple of months ago. I'll start by showing the end product, and then showing the part that I found to be the trickiest. 

<iframe src="https://giphy.com/embed/7JgtecaHxnpGsLhxe5" width="480" height="242" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/7JgtecaHxnpGsLhxe5"></a></p>

As the demonstrated, as the user types a character into the input, each character shows up below in a separate inline box. When a user clicks one of these characters, the same character (including the same order!) should be deleted in the input box above. 

Each character is a separate, presentational `Character` component. This is what the user must click in order to remove the corresponding character from the input.

```jsx
import React, { Component } from "react";

class Character extends Component {
  render() {
    const char = this.props.char;

    return (
      <li onClick={this.props.onClick}>
        {char}
      </li>
    );
  }
}

export default Character;
```

The challenge was figuring out how to ensure that when a user clicks the presentational `Character` component, it updates the stateful `App` component. 

```jsx
import React, { Component } from "react";
import CharComponent from "./CharComponent";
import "./App.css";

class App extends Component {
  constructor(props) {
    super(props);
    this.state = { value: "", textLength: 0 };
  }

  handleChange = (event) => {
    this.setState({
      value: event.target.value,
      textLength: this.state.value.length
    });
  };

  handleClick = (index) => {
    const arrayOfChars = this.state.value.split("");
    const result = arrayOfChars.filter((char, i) => i !== index).join("");

    this.setState({
      value: result
    });
  };

  render() {
    const chars = this.state.value.split("");

    const charItems = chars.map((char, index) => (
      <Character
        key={index}
        char={char}
        onClick={this.handleClick.bind(null, index)}
      />
    ));

    return (
      <div className="App">
        <form onSubmit={this.handleSubmit}>
          <label>
            Type some input here:
            <input
              type="text"
              name="name"
              value={this.state.value}
              onChange={this.handleChange}
            />
          </label>
          <input type="submit" value="Submit" />
        </form>
        <div>
          <p>{this.state.value}</p>
          {charItems}
        </div>
      </div>
    );
  }
}

export default App;
```
I added the `handleClick` method to the `App` component because it was the stateful component (the state is the input text) that needed to updated when a user clicked a character. This method filtered through all the characters in the input box, and removed the character with an index that matched the index of the clicked character. In order for this to work, I had to bind the index to the `handleClick` function called inside of the `Character` component: 

`<Character key={index} char={char} onClick={this.handleClick.bind(null, index)}/>`.

`bind()` applies the index that is set when the `charItems` array is mapped to the same index that is applied in the `handleClick` method. This is how we are sure that when a user clicks a `Character` component, the corresponding character will be deleted in the input box.

```jsx
    const chars = this.state.value.split("");

    const charItems = chars.map((char, index) => (
      <Character
        key={index}
        char={char}
        onClick={this.handleClick.bind(null, index)}
      />
    ));
```

Notice, that the second argument for `bind()` is `null`: 

`onClick={this.handleClick.bind(null, index)}`.

 I provide the function with fewer arguments than it expects, which is known as partial application of function

`bind()` does 2 things: 1) it can bind the function's context to an object, 2) it can fill arguments with given values from left to right. In this blog's example, it was filled with the value of `index`.

If I did not utilize the fat arrow syntax in the click handler, I would've had to address the first functionality above by binding the context to `this`. However, because I used the fat arrow syntax in my click handler, I was able to pass in `null` instead of `this`.

```jsx
  handleClick = (index) => {
    const arrayOfChars = this.state.value.split("");
    const result = arrayOfChars.filter((char, i) => i !== index).join("");

    this.setState({
      value: result
    });
  };
  ```

There are probably other ways to accomplish this challenge, but I enjoyed learning more about `bind()` through this process. Also in case you noticed the details in the demo, I removed some of the code (used for styling and validating the input), so that I could focus just on the issue around deleting characters. If you are interested in the input character count or conditional output works shown in the demo, you can view all the code on Github [here](https://github.com/hcarnes/assignment_2/tree/master/src).