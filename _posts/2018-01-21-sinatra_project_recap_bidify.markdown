---
layout: post
title:      "Sinatra Project Recap: Bidify"
date:       2018-01-22 02:41:18 +0000
permalink:  sinatra_project_recap_bidify
---

I'm a minimalist, so I'm usually looking for creative ways to get rid of items rather than keep track of them. With that said, for my Sinatra Portfolio Project, I created a CRUD app that keeps track of auction items. I started out by reviewing the MVC framework and building [Google sheets](https://docs.google.com/spreadsheets/d/10BFNl0vXOAINvCTHzDdtOTy6o6FXCwNfQv0l7L_e3KU) to organize my ideas around routes and object relationships. 
## Custom Validations
Validations keep data you don’t want out of the database. So, if you don’t want people signing up for your app without a username, you could use an ActiveRecord validation and include it in the pertinent class: 
```ruby
class User < ActiveRecord::Base
  validates :username, presence: true
end
```
However, you might need to validate data in ways that are not as common as something like validating the presence of a username. In that case, you would need to create a custom validation. For example, Bidify allows users to list auction items, as well as bid on other people’s items. To ensure users can’t bid on their own item, I created a custom validation. I added `validate :no_self_bidding` to the `Bid` class and created a `no_self_bidding` method in the same class. To make a custom validation, you write a simple logical test, such as `if self.user == self.listing.user`, which defines the case where you want there to be a validation failure. To add the error, you call `errors.add`, where the first parameter is the attribute associated with the error, and the second is the message you want presented to the user. In the case where your error is not related to a single attribute, you can use `:base` which you'll see below in `auction_isnt_over`:

```ruby
class Bid < ActiveRecord::Base
 belongs_to :user
 belongs_to :listing
 
 validates :user_id, presence: true
 validates :listing_id, presence: true
 validate :no_self_bidding
 validate :amount_higher_than_current_price
 validate :auction_isnt_over

 def no_self_bidding
   if self.user == self.listing.user
     errors.add(:user_id, "can't bid on your own item :)")
   end   
 end

 def amount_higher_than_current_price
   if !self.amount || self.listing.current_price > self.amount
     errors.add(:amount, "must be higher than current price")
   end
 end

 def auction_isnt_over
   # if end time is in the past, add error
   if self.listing.over?
     errors.add(:base, "auction is over")
   end
 end
end
```
Happy validating!
![example video](https://i.imgur.com/Wsgc0ma.gif)

