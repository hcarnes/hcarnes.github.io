---
layout: post
title:      "jQuery Front End Project Recap: Static Methods"
date:       2018-03-27 13:13:29 -0400
permalink:  jquery_front_end_project_recap_static_methods
---


For this project, I was required to add dynamic features that are possible only through jQuery and a JSON API to my previous Rails app, [Socratic](https://github.com/hcarnes/socratic/tree/jquery_project). After working through the requirements, I had some class-specific logic that was cluttering my JS files AKA [spaghetti code](https://en.wikipedia.org/wiki/Spaghetti_code). To clean things up, I moved this logic into the class files by using static methods.

----
## What is a static method?

> According to MDN, static method calls are made directly on the class and are not callable on instances of the class. Static methods are often used to create utility functions.

Static methods in JavaScript are similar to class methods in Ruby. Introduced in ES6, the functionality of a static method belongs to an entire class, as opposed to a particular instance of a class.  You can create a static method by prefixing `static` to the definition of the method function. Below, you can see the static method: `all()`, which fetches all of the tags in the class Tag.

```javascript
class Tag {

  constructor(tagAttributes) {
    Object.assign(this, tagAttributes)
  }

  static async all() {
    const tagsResponse = await fetch(`/tags`, {
      headers: {
        'Accept': 'application/json'
      },
      credentials: 'same-origin'
    })
    const tags = (await tagsResponse.json()).map(tagObject => new Tag(tagObject))
    return tags
  }

}
```

If you drop into the console and type in `await Tag.all()`, you will see the method returns all of the instances of Tag.

![Imgur](https://i.imgur.com/EUnLhTx.png)

In Ruby, to acheive the same functionality, you could prefix the method with `self`. For example, the following code will return an array of instances:

```ruby

class Tag

  def self.all
    db.execute("SELECT * FROM tags;").map {|tag_attributes| Tag.new(tag_attributes)}
  end
	
end

```

## Using static methods

In the `questions.js` file, we can see how we are able to utilize the static method `all()` to update the full list of tags for the user. 

```javascript
  async function updateTagList() {
    const tags = await Tag.all()
    const optionsHtml = tags.map(tag => tag.asOptionHtml()).join("")

    $('#tag-list').html(optionsHtml)
  }
```

Click the demo below to see the static method at work throughout the app:

<a href="https://www.youtube.com/watch?v=zxUt6a8NFxQ">
<img border="0" alt="example" src="https://i.imgur.com/sir8kmP.gif" width="600" height="600">

>The full list of project requirements are listed [here](https://github.com/learn-co-students/rails-js-assessment-v-000/blob/master/README.md). 

	
	
	
	
	
	
	
	
	
	
	
