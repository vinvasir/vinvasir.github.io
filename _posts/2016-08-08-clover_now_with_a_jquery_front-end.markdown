---
layout: post
title:  "Clover: Now with a JQuery front-end!"
date:   2016-08-08 02:15:07 +0000
---


As promised, I'm back with an update to my Rails app, Clover. The app still does everything I described in my [previous post](https://vinvasir.github.io/2016/07/09/clover_my_rails_app_for_project_blogging_and_collaboration/), but now loads, updates, and formats most of its data through javascript. This means there are fewer areas where you need to refresh the page, which should add up to a smoother user experience!

To create an API for my front-end javascript, I used the gem for ActiveModel Serializers. I then generated serializers for my project, note, and user models. I wanted to make my JSON APIs include references to other models, so I added some extra serializers to avoid an infinite recursion problem. For my ProjectSerializer, the end result was this: 

```
class ProjectSerializer < ActiveModel::Serializer
  attributes :id, :title, :description, :public, :commenters
  has_one :category, serializer: ProjectCategorySerializer
  has_one :user, serializer: ProjectUserSerializer
  has_many :creator_updates, serializer: ProjectCreatorUpdateSerializer
  has_many :comments, serializer: ProjectCommentSerializer
end
```

As a result, I was able to use javascript to load all of the data I needed for both my project index page and show page. I then created Javascript Model Objects to encourage code reuse and separation of concerns. These objects would keep track of the data for projects and notes, as well as include behavior for formatting the data into html and rendering it on the page. For example, my Project object kept track of the following data: 

```
function Project(id, title, description, user, commenters, category, public){
	this.id = id;
	this.title = title;
	this.description = description;
	this.user = user;
	this.commenters = commenters;
	this.category = category;
	this.public = public;
}
```

To include actual behavior in my objects, I used prototypical inheritance so that I wouldn't have to create a new function in memory each time I instantiated an object. My Project html formatter ended up looking like this:

```
Project.prototype.formatUpdate = function(){
	var projectDiv = "";
	
	projectDiv += "<h2><a href='/projects/" + this.id + "'>" + this.title + "</a></h2>";
	projectDiv += "<strong class='category'>Category: " + this.category.name + "</strong><i id='cat-desc-" + this.id + "'></i><br>";
	projectDiv += "<strong class='creator'>Creator: " + this.user.email + "</strong><br>";

	projectDiv += "<p class='commenters'>Commenters: ";
	this.commenters.forEach(function(c){
		projectDiv += c.email + " | ";
	});
	projectDiv += "</p><br><br>";

	projectDiv += "<strong>Project Description:</strong><i id='description-"+ this.id + "'>" + this.description.substring(0, 27) +"...</i>";

	var public = this.public ? 'yes' : 'no';
	projectDiv += "<br><p>Visible to others: <span class='visibility'>" + public + "</span></p>";

	this.renderUpdate(projectDiv);
};
```

With these Object Models taken care of for both my projects and notes, I was able to create a separate file focused on API requests and responses. After receiving an API response, I would instantiate new project and note objects and then call those objects' functions for formatting. For example, my index page just needed to make one simple request before calling my Project model's formatter.

```
function getAllProjects(){
	$.get("/projects.json", function(data){
		data.forEach(function(projectJSON){
			var p = new Project(projectJSON["id"], projectJSON["title"], projectJSON["description"],projectJSON["user"], projectJSON["commenters"], projectJSON["category"], projectJSON["public"]);
			p.formatUpdate();
		});
	});
}
```

Surprisingly, one of the hardest aspects of this project was setting up the function calls so that they would occur after each page was loaded. At first, I found that my javascript wouldn't load unless I entered a URL in my browser or hit the refresh button. If I clicked on a link like a normal user, I just saw empty templates without any content. 

After some googling, I concluded that turbolinks was preventing jquery's $(document).ready() method from working properly. I tried a common workaround with the following snippet:

```
var ready = function(){
  // my main function calls and event listeners here
}
$(document).on('turbolinks:load', ready);
```

But I found that this solution started making my forms submit data twice! I ended up settling instead on the jquery.turbolinks gem, which allowed me to use the $(document).ready function as I originally had it.

I also used a code snippet for extending jquery's .ready() method, so that it would work with a page-specific javascript css selector. This extension allowed me to make page-specific javascript calls by adding controller and action names to the body element on all my pages.

```
#app/views/layouts/application.html.erb

<body class="<%= controller_name %> <%= action_name %>">

#app/assets/javascripts/ui.js

var ready = function(){

	$(".projects.index").ready(function(){
		getAllProjects();
		console.log("Here's your index-page-only javascript!");
		createProject();
	});

	
	$(".projects.show").ready(function(){
		var id = parseInt(window.location.pathname.split("/").pop());
		getOneProject();

		$(".button-link.edit").replaceWith("<button class='button-link edit'>Edit Project</button>");
		$("button.button-link.edit").on("click", function(){
			renderEditForm();
		});
		$("button.button-link.note").on("click", function(){
			renderNotesForm();
		})

		updateProject(id);
		addNote(id);
	});

	$(".users.show").ready(function(){
		getUserProjects();
	})

};

$(document).ready(ready);
$(document).on('turbolinks:load', ready);
```

With this javascript in place, I now have a lighter Rails API that focuses on the business logic in the Model, with presentation logic handled mostly in the browser. I'll continue tinkering with clover, especially to make sure that separation-of-concerns in raw javascript clicks for me. For now, you can check out the code on [github](https://github.com/vinvasir/clover/), and stay tuned for further updates!
