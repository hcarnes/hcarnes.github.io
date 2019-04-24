---
layout: post
title: "Details Oriented"
date: 2019-4-23 00:09:15 -0500
permalink: details_oriented
---

You don't need JavaScript to implement a dropdown. You can create an accessible dropdown using just the html `<details>` element. This post will cover how I implemented a dropdown menu for an app I recently created called [Congress Connect](https://github.com/hcarnes/congress_connect). I first learned about this element at a BrooklynJS meetup almost a year ago. There was a really cool talk, and the [slides](https://github.com/muan/details-on-details) are excellent, and I highly recommend reading them if you are interested in learning more than this post covers.

## How it works

To implement the dropdown, start with the `<details>` tag.

_According to [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details), the (`<details>`) creates a disclosure widget in which information is visible only when the widget is toggled into an "open" state._

Inside that, nest the `<summary>` tag, which holds the visible label of the information that can be viewed. Below that, include the content that you'd like to disclose. Here's an example:

```html
<details>
  <summary>Voting Record</summary>
  <p>
    Here is the information that shows after the summary element is clicked!
  </p>
</details>
```

## Here's a demo from Congress Connect

<a href="https://imgur.com/ZYLCkOo"><img src="https://i.imgur.com/ZYLCkOo.gif" title="drop down menu demo" /></a>
Here's a snippet of the code to give you another example:

```jsx
<>
  <details>
    <summary>Voting Record</summary>
    {member.roles
      .filter(x => x.congress === "116")
      .map(role => (
        <ul>
          <li>Bills sponsored: {role.bills_sponsored}</li>
          <li>Bills cosponsored: {role.bills_cosponsored}</li>
          <li>Missed votes: {role.missed_votes_pct}%</li>
          <li>Votes with party: {role.votes_with_party_pct}%</li>
        </ul>
      ))}
  </details>
</>
```

You can checkout the full implementation [here](https://github.com/hcarnes/congress_connect).

## Other notes of interest

- To implement an accessible dropdown, you don't need to rely on aria attributes.
- `<details>` is semantic HTML, so its screen reader friendly.
- The child element, `<summary>`, is focusable out of the box.
- This element [supported](https://caniuse.com/#search=details) in Safari, Chrome, and Firefox. Sorry, no IE Edge.
- You can also use `<details>` for accordians and popovers.
- The author of the original `<details>` talk that inspired me to use it in my own work recently published slides for another talk on the powers of the `<dialog>` element:
  <blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">My slides for “A dialog on &lt;dialog&gt;” at <a href="https://twitter.com/brooklyn_js?ref_src=twsrc%5Etfw">@brooklyn_js</a> tonight are here!  <a href="https://t.co/Wo3ftEKx83">https://t.co/Wo3ftEKx83</a><br><br>I also added speaker notes to the slides for “Details on &lt;details&gt;” from last year: <a href="https://t.co/OrDeTXsKgs">https://t.co/OrDeTXsKgs</a><br><br>💾💾💾</p>&mdash; Learn HTML (@muanchiou) <a href="https://twitter.com/muanchiou/status/1119091599474958336?ref_src=twsrc%5Etfw">April 19, 2019</a></blockquote>
  <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
