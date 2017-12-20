---
layout: post
title:      "Hannahoke"
date:       2017-12-19 23:06:00 -0500
permalink:  hannaoke

---
I don't always have time to karaoke as much as I would like, but I did carve out time to create a KaraokeBar class. It was a fun way to implement the concept of private methods and review more OO Ruby.  

Each instance of KaraokeBar is initialized with an instance variable of `@queue = []` to store singers as they sign up and `@song_history` to store each song that has already been performed. There is also an `attr_reader` for `:queue`, so that we can add singers to the queue and get access to the array of singers.

```ruby
class KaraokeBar
  attr_reader :queue

  def initialize
    @queue = []
    @song_history = []
 end
  ```
As each person signs up, they provide their name, the song name, and the song's artist name. The `add_singer` method checks to see if a requested song has been played recently or if someone else in the queue has chosen that song. If the song is a duplicate, the hopeful singer will be asked to choose a new song. If they picked a new song, the singer's information gets added to the queue. They also receive a message about their status in the line.

  ```ruby 
  def add_singer(singer_name, song_name, artist)
    if unavailable_songs.include?(song_name)
      puts "That song is unavailable. Please choose a new song."
    else
      @queue << {singer_name: singer_name, song_name: song_name, artist: artist}
      puts "Thanks, #{singer_name}. You are number #{queue.length} in line."
    end
  end
  ```
The `on_deck` method let's the next person know they need to prepare to hit the stage. `@queue.first` is set to a new variable, so that we can access the hash of singer information and call the next singer by  name. 

```ruby 
  def on_deck
    next_singer = @queue.first
    puts "#{next_singer[:singer_name]}, you are up next!"
  end
  ```
The `call_singer`method checks if anyone is in the queue and removes the first person from the queue. It also prepends their song to the `@song_history` array. Next, a search query is passed to Google I'm Feeling Lucky, which opens a youtube video of the song with the karaoke lyrics.

``` ruby
  def call_singer
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
The method `unavailable songs` cannot be called outside of an instance of KaraokeBar because it is only used internally within the `add_singer` method and is marked private. This method checks to ensure that a song cannot be sung if it has been sang within the last 10 songs and that someone has not already signed up for that song.

  ```ruby 
  private
  def unavailable_songs
    (@song_history[0..9]+@queue).map do |singer|
      singer[:song_name]
    end
  end
end
```
Worst case scenario: we have to hear "Love Shack" one time.
