---
layout: post
title:  "My Sinatra Comic Book Reviews Site"
date:   2016-07-01 16:05:11 +0000
---


I've just completed a web app for reviewing comic books, built in Sinatra. 

I've always had a casual interest in comic books, but in the past few months I've felt a real need to delve deeper into them. For the first time in my life, I've had access to a brick-and-mortar comic book store, filled with piles of trade paperbacks and omnibuses from the DC and Marvel universes. Now, comic books no longer feel like some relic that I can reminisce about while reading Kavalier and Clay: this store is one of the centerpieces of my neighborhood! 

So I needed a site where I could post some simple reviews and notes about different comic books, and organize them by author, artist, and genre. This sounded like the perfect chance to practice using some ActiveRecord associations in a CRUD app, so I set to work creating a Sinatra project from scratch. 

To configure the folder structure, I took some boilerplate code from tutorials I had done. I made sure to have a Gemfile that included Sinatra, ActiveRecord, Sinatra-ActiveRecord, and Rake. I used bcrypt for password encryption, pry for debugging, and the Sinatra Shotgun server so I could see my changes with a simple refresh, instead of having to restart the server. To tie things together, I made my config.ru file load an environment in config/environment.rb. This file set up my SQLite database with ActiveRecord and loaded my "app" folder with its MVC organization. 

```
ENV['SINATRA_ENV'] ||= "development"

require 'bundler/setup'
Bundler.require(:default, ENV['SINATRA_ENV'])

ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/#{ENV['SINATRA_ENV']}.sqlite"
)

require_all 'app'
require_all 'lib'
```

Over time, I added tables to my database for all the basic elements of a comic book. My starting point was a comic_books table with a title and description, along with foreign keys for the book's primary writer and artist. This established a basic relationship in which a book would belong to an author and artist, while authors and artists would have many books. It also left room for a book to have_many reviews and genres. But I didn't want genres to be associated with just one book, so I made a book_genres table. As a result, a book would have_many genres through book_genres, and a genre would have many books through book_generes. Based on this database design, my final ComicBook model look like this:

```
class ComicBook < ActiveRecord::Base
  belongs_to :author
  belongs_to :artist
  has_many :reviews
  has_many :book_genres
  has_many :genres, through: :book_genres
  validates_presence_of :title, :description    
end
```

Of course, I wasn't including all these relationships just to play around with ActiveRecord and SQL. My goal, instead, was to let users view comic books from multiple perspectives. For example, if they wanted to see all the books in a genre, they could click on a link to view all genres, and then click on links to each comic book from there. To accomplsih this, I created an index page for comic books, genres, artists, and authors. This meant that all of these models needed their own controller. Additionally, I needed a way for users to submit reviews, so I created a reviews controller as well. 

Many of my models required validations to ensure that users didn't enter empty data. For example, my ComicBook model, shown above, validated the presence of a title and description field. My controller then checked to see if the ComicBook was saved properly, which could only happen if a user filled in the required fields. I used the following conditional logic: 

```
    @comic_book = ComicBook.new(title: params["title"], 
                                  description: params["description"],
                                  author: author,
                                  genre_ids: params["genres"])
    if @comic_book.save
      redirect "/comic_books/#{@comic_book.id}"
    else
      flash[:error] = "Please fill out all fields!"
      redirect "/comic_books/new"
    end
```

Ultimately, reviews were at the heart of my app, but because of all the data handled in the comic book controller and model, my reviews controller was able to be leaner. I only included a separate page for editing an individual review. Review creation, on the other hand, was handled from the comic book's show page, where a form would post to the following controller action below. This code checked to see if a user was logged in and posted valid data, and created the necessary error messages for my views.  

```
  post '/reviews/:comic_book_id' do
    if session[:user_id]
      @comic_book = ComicBook.find(params[:comic_book_id])
      review = @comic_book.reviews.new(content: params["content"], 
                                  rating: params["rating"],
                                  user_id: params["user_id"])
      if review.save
        flash[:error] = "Successfully added review."
        redirect "/comic_books/#{@comic_book.id}"
      else
        flash[:error] = "Please enter both a review and rating."
        redirect "/comic_books/#{@comic_book.id}"
      end
    else
      flash[:error] = "You need to be logged in to add a review!"
      redirect '/login'
    end
  end
```

To tie it all together in a clean, readable GUI, I used a layout.erb file and a master stylesheet in my public/stylesheets folder. The layout file contained a navbar created through raw html and css, courtesy of W3Schools. 

```
*app/views/layout.erb*

    <ul class="top-nav">
        <li><a href="/">Home</a></li>
        <li><a href="/comic_books">All Comic Books</a></li>
        <li><a href="/comic_books/new">Add a Comic Book</a></li>
        <li><a href="/authors">Authors</a></li>
        <li><a href="/genres">Genres</a></li>
        <% if session[:user_id].nil? %>
            <li class="right"><a href="/signup">Sign Up</a></li>
            <li class="right"><a href="/login">Log In</a></li>
        <% else %>
            <li class="right"><a href="/logout">Log Out</a></li>        
            <li class="right"><a href="/users/<%= session[:user_id] %>">Your Profile</a></li>
        <% end %>
    </ul>
        

*public/stylesheets/master.css*

/* top navbar */

ul.top-nav {
    list-style-type: none;
    margin: 0;
    padding: 0;
    overflow: hidden;
    background: url('/images/stardust.png');
}

.top-nav li {
    float: left;
}

.top-nav li.right {
    float: right;
}

.top-nav li a {
    display: block;
    color: white;
    text-align: center;
    padding: 14px 16px;
    text-decoration: none;
}

/* Change the link color to #111 (black) on hover */
li a:hover {
    background-color: #111;
}
```

Finally, I used a background from the subtlepatterns website, which I included in my assets folder and loaded in my stylesheet. The final result was a CRUD app with a few simple different options for learning about comic books: perfect for my foray into a new hobby! As I use the app, I plan to continue adding features and refactoring. You can check it out on [github](https://github.com/vinvasir/cbook-sinatra) and/or watch my [youtube demo](https://www.youtube.com/watch?v=Mm4Os3pr2x8&feature=youtu.be).

