---
layout: post
title:      "Artsy Scraper CLI"
date:       2017-12-22 20:06:00 -0500
permalink:  artsy_scraper_cli
published:  false
---

# Artsy Scraper CLI

For my CLI Data Gem project, I created the [ArtsyScraperCli](https://github.com/hcarnes/artsy_scraper_cli) gem. This gem is a virtual art gallery that lists artwork genes and prints 10 works of art based on the user's selection. Data for this gem is pulled from [Artsy.com](https://www.artsy.net/collect) using Selenium Webdriver. I chose [Selenium Webdriver](http://blog.morizyun.com/blog/selenium-webdriver-ruby-browser-test/index.html) to scrape data because Arty's website used Javascript to render html. I also used a the gem [iterm-imgcat](https://rubygems.org/gems/iterm-imgcat) to print each artwork image in iterm.

The greatest challenge was choosing an object oriented design. I started out with 3 classes: Controller, Gene, and Artwork. After stubbing out some code and researching OO design patterns, I decided to add a Scraper class that would handle scraping both the genes and artworks. It helps me to draw out my ideas, especially since I've hit OO Ruby.

![oo_notes](https://i.imgur.com/DD7UhGi.jpg "oo_notes")

## Debugging the Scraper

Scraping with Selenium Webdriver was a great learning experience. The `initialize` method starts a browser in the background and connects to it. The `scrape_genes` method gets the artsy website and returns all the elements of the class that contains the gene names.

```ruby
class Scraper

  def initialize
    options = Selenium::WebDriver::Chrome::Options.new(args: ['headless'])
    @driver = Selenium::WebDriver.for(:chrome, options: options)
  end

  def scrape_genes
    @driver.get('https://www.artsy.net/collect')
    elements = @driver.find_elements(css: ".cf-categories__category")
    elements.map do |gene_element|
      Gene.new(gene_element.text, gene_element)
    end
  end
  ```

While trying to drill down and scrape the artworks from each gene's page, I ran into an error I had never seen: 

`stale element reference: element is not attached to the page document Selenium::WebDriver::Error::StaleElementReferenceError`

It turns out that when Selenium Webdriver clicked on the gene element in order to scrape the artworks from the selected gene page, the page wasn't loading fast enough. I added a `while` loop that would check for the `data-loading` css selector and let the user know the page was still loading. In other words, if any of the elements on the page have an attribute of `data-loading`, Ruby will wait on scraping the artwork data.

```ruby
  def scrape_gene_artworks(gene)
    gene.element.click
    #wait for page to load 
    while @driver.find_elements(css: ".cf-artworks[data-loading='true']").any?
      #wait before checking again
      puts "The list of #{gene.name} artworks is loading..."
      sleep 0.1
    end
    art_elements = @driver.find_elements(css: ".artwork-item")
    art_elements.map do |art_element|
      title = art_element.find_element(css: ".artwork-item-title").text
      artist_name = art_element.find_element(css: ".artwork-item-artist").text
      image_url = art_element.find_element(css: "img").attribute("src")
      Artwork.new(title, artist_name, image_url)
    end
  end
```
OO Ruby really slowed down my momentum as far as finishing lessons and labs, but it helped me learned a lot about slowing down in order to let my creativity and curiousity guide my coding journey. Printing artwork in iterm was cool.

![iterm_image](https://imgur.com/898o2Te.gif "Screenshot")







