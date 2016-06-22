---
layout: post
title:  "My First RUBY CLI GEM"
date:   2016-06-22 15:12:09 +0000
---


As one of my first projects in the Flatiron Learn-Verified program, I built a gem that allows users to see free art exhibits on the command line. The project was a great opportunity to practice object encapsulation and model and controller concepts, without all the "magic" I had seen in the Rails framework. 

To load data into my gem, I needed to find either a public API or a website that I could scrape without major issues. I also needed to have data that went at least one level deeper, to give more details about each item. After some searching, I felt that there weren't any APIs that met my requirements and didn't require a key. I then searched for scrapable websites. I tried the Met's list of current exhibits at first, but I saw that its frontend was written in Angular, and I didn't want to deal with the extra scraping issues that would present. As I walked to the Subway in disappointment, I did what I normally do when I feel to spaced-out to read anything too dense: I picked up a free Timeout issue near the entrance. Soon I realized that their event listings were at the perfect level of generality to read from the command line. Feeling more hopeful this time, I checked out their website at home and found that it was perfect: simple html on the frontend, with a link for each exhibit that would provide additional details about its venue and hours.

At first, my biggest stumbling block was the folder structure. After some googling, however, I found the bundler command for automatically generating a gem. I then set out the basic architecture: a Scraper class and Exhibit class that would serve as models with the bulk of the business logic; a CLI class that would serve as a controller for user input; and a binary executable file from which the user could start the program. 

From the start, my Scraper class was only responsible for taking data from Timeout's free-art index page and calling the Exhibit class's methods with that data. The class had three core methods: 

```
+  def get_page
 +    Nokogiri::HTML(open("http://www.timeout.com/newyork/art/the-best-free-art-exhibitions-in-nyc"))
 +  end
 +  
 +  def scrape_index_page
 +    page = get_page.css("article.feature-item")
 +  end
 +  
 +  def make_exhibits
 +      scrape_index_page.each do |exhibit|
 +          e = Exhibit.new_from_index_page(exhibit)
 +      end
 +  end
````

My Exhibit class was responsible for instantiating new objects containing exhibit data, starting with the exhibit's title, URL, and description. I also made the class keep track of all the objects it instantiated.

```
> +class Exhibit
 +    attr_accessor :url,:title, :description
 +    @@all = []
 +    
 +    def self.new_from_index_page(exhibit)
 +      self.new(
 +          "http://www.timeout.com" + exhibit.css("a.read-more").attr("href").value + "#tab_panel_2",
 +          exhibit.css("h3 a").text,
 +          exhibit.css("p.feature_item__annotation--truncated")
 +          ) 
 +    end
 +   
 +   def initialize(url, title, description)
 +      @url = url
 +      @title = title
 +      @description = description
 +      @@all << self
 +   end
 +   
 +   def self.all
 +       @@all
 +   end
 +   
 +   def self.find(id)
 +      self.all[id-1] 
 +   end
 +en
```


My CLI contained a simple #call method, which asks the Scraper to make new exhibits and then calls the #start method. In #start, the user is prompted for which exhibits they would like to see, and then the method calls the #print_exhibits action. If the user wants to see another exhibit, #start calls itself recursively. 

My executable file was even simpler:

```
#!/usr/bin/env ruby

require_relative '../lib/free_art_exhibits'

FreeArtExhibits::CLI.new.call
```

After getting this core functionality sorted out, I started implementing more conventions to make the gem publishable. For example, I made sure to namespace all my classes. They would now be called FreeArtExhibits::CLI, FreeArtExhibits::Scraper, and FreeArtExhibits::Exhibit. At first, this change stopped my program from running, but I fixed it by loading the proper environment in lib/free_art_exhibits.rb

```
require 'open-uri'
require 'pry'
require 'nokogiri'

require_relative "./free_art_exhibits/version"
require_relative "./free_art_exhibits/cli"
require_relative "./free_art_exhibits/exhibit"
require_relative "./free_art_exhibits/scraper"

module FreeArtExhibits
end
```

At this point, I had all the basics worked out, and simply added some more methods for gathering more user input and reading each exhibit's venue, hours, and website. To learn more about the project, you can check out the [repo on github](https://github.com/vinvasir/free_art_exhibits).
 
 
