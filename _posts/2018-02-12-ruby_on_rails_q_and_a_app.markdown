---
layout: post
title:      "Ruby on Rails Q&A App"
date:       2018-02-12 19:44:19 +0000
permalink:  ruby_on_rails_q_and_a_app
---


Keeping track of how things are supposed to work at work is hard. Nobody likes getting asked the same question over and over, especially ones that probably have answers buried in Slack or old emails. [Socratic](https://github.com/hcarnes/socratic) attempts to solve this problem. This app allows users to post questions, allows multiple users to answer one question, and allows users to mark the most helpful answer with a green check mark.

One of the biggest hurdles I faced during this project was meeting the following requirement:  You must include a nested form that writes to an associated model through a custom attribute writer.

A nested form handles more than one model object at a time. With nested forms, users can submit one form, as opposed to a creating new form for each model object. In my case, I used a nested form that allows users to submit a question and tags on the same form. 

## Setting Up Object Relationshps

Because questions can have multiple tags and tags can belong to multiple questions, I created the join table, QuestionTags. You can see how I mapped this `has_many :through`, along with all the other relationshpips[ here](https://docs.google.com/spreadsheets/d/1DLd3Y9fC0-vweIqquSyq3wj5PhdbTabyXca-roWh__4/edit#gid=2001163121). 

## Creating a Nest Form

Next, I added the `tag_name` attribute in question form:

```ruby
<%= form_for @question do |f| %>
  <%= f.label :summary %>
  <%= f.text_field :summary %>

  <%= f.label :tag_names, "Tags" %>
  <%= f.text_field :tag_names, class: "form-control" %>

  <%= f.label :content, "Question" %>
  <%= f.text_area :content %>
  <%= f.submit class:  %>
<% end %>
```
## Updating Strong Params

I also added the new custom associated attribute to the permitted params in the `QuestionsController`. Now, our newly created attribute is on the model's whitelist of accepted attributes.

```ruby
class QuestionsController < ApplicationController
  ...
  private

  def permitted_question_params
    params.require(:question).permit(:content, :summary, :tag_names)
  end
end
```
## Attribute Customization

I also needed to create the associations in the parent model. In this case, I created readers and writers for `tag_names` in the Question model. Users can enter tags, such as `coffee, snacks` and the custom writer will check the database to ensure that duplicate tags are not created through the `find_or_initialize_by` method.

```ruby
class Question < ApplicationRecord
  has_many :question_tags
  has_many :tags, through: :question_tags

  validates_presence_of :content, message: "Empty questions are not virtuous"
  validates_presence_of :summary, message: "Brevity is the soul of wit, so summary is required"

  def tag_names
    self.tags.map(&:name).join(", ")
  end

  def tag_names=(names)
    self.tags = names.split(",").map(&:strip).map do |name|
      Tag.find_or_initialize_by(name: name)
    end
  end
end
```

The form looks simple, but as you can see, there is a lot going on behind the scenes.

<a href="https://imgur.com/xip4utG"><img src="https://i.imgur.com/xip4utG.png" title="source: imgur.com" height="500" width="250" /></a>


