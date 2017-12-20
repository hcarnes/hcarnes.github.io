---
layout: post
title:      "Hannaoke"
date:       2017-12-19 23:06:00 -0500
permalink:  hannaoke
---
I don't always have time to karaoke as much as I would like, but I did carve out time to create a KaraokeBar class. It was a fun way to implement the concept of private methods and review more OO Ruby.  

Each instance of KaraokeBar is initialized with `@queue = []` to store singers as they sign up and `@song_history` to store each song chosen. There is also an `attr_reader` for `:queue`, so that we can add singers to the queue and get access to the array of singers.

```ruby
class KaraokeBar
  attr_reader :queue

  def initialize
    @queue = []
    @song_history = []
 end
  ```
As each person signs up, they provide their name, the song name, and an artist name. The `add_singer` method checks to see if a requested song is already chosen by calling the private `unavailabe_songs` method. If a song has already been chosen by someone else, the private method `unavailable_songs` will catch the duplicate and tell the hopeful singer to choose another song. If they picked a fresh, new song, the singer's information gets added to the queue as a hash, and then the receives a personalized messsage letting them know what number they are in line.

  ```ruby def add_singer(singer_name, song_name, artist)
    if unavailable_songs.include?(song_name)
      puts "That song is unavailable. Please choose a new song."
    else
      @queue << {singer_name: singer_name, song_name: song_name, artist: artist}
      puts "Thanks, #{singer_name}. You are number #{queue.length} in line."
    end
  end
  ```
The `on-deck` method let's the next person know they need to get ready to hit the stage. There is no turning back. The `@queue.first` is set to a new variable, so that we can access the hash of singer information and call the next singer by  name. 

```def on_deck
    next_singer = @queue.first
    puts "#{next_singer[:singer_name]}, you are up next!"
  end
  ```

```ruby def call_singer
    if @queue.any?
      new_singer = @queue.shift
      @song_history.unshift(new_singer)
      query = "#{new_singer[:artist].gsub(/\s/, '+')} #{new_singer[:song_name].gsub(/\s/, '+')} karaoke"
      `open "https://www.google.com/search?q=#{query}&oq=#{query}&btnI=1"`
      puts "Step right up #{new_singer[:singer_name]}!!!!"
    else
      puts "Attract more customers, nobody is singing."
    end
  end
  ```
The private method `unavailable songs` cannot be called outside of an instance of KaraokeBar because it is only used internally within the `add_singer` method. Worst case scenario: we have to hear "Love Shack" one time.

  ```ruby private
  def unavailable_songs
    (@song_history[0..9]+@queue).map do |singer|
      singer[:song_name]
    end
  end
end
```
