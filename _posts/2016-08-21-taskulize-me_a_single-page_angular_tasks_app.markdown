---
layout: post
title:  "Taskulize-me: A single-page Angular tasks app"
date:   2016-08-21 01:53:56 +0000
---


For my first complete Angular Single-Page App, I've created a task manager called Taskulize-me. 

The app allows registered users to create tasks, view them in both a list mode and a calendar mode, and add notes to their tasks. Filters are also available to help users prioritize the tasks they see, such as ones that are past-due.

I built Taskulize-me by creating a Rails API to provide my Angular front-end with data. I ran the "rails new" comman and saw my folder structure come into place as usual. So far so good, except this time Rails 5 was the default version, meaning I got to use ActiveModel serializers out of the box!

My task model ended up being pretty simple, so I could focus on using Angular best practices instead of configuring my backend. I made fields for the title, start and end times, and a description. I used a serializer to make all these attributes available in the API, which would only provide json responses. My task controller ended up with the following code:

```
	def index
		if current_user
			@tasks = current_user.tasks
			render json: @tasks
		else
			render json: {error: "You must be logged in!"}
		end
	end

	def new
		@task = current_user.tasks.build
	end

	def create
		@task = current_user.tasks.build(task_params)
    if @task.save
      render json: @task, status: 201
    else
      render json: @task.errors, status: :unprocessable_entity
    end
	end

	def show
		render json: @task
	end

	def edit
	end

	def update
		return unless @task.user == current_user
		if @task.update(task_params)
			render json: @task, status: 201
		else
			render json: @task.errors, status: :unprocessable_entity
		end
	end
```

With this simple task model in place, I was able to create an easily reusable component. 

```
var task = {
	templateUrl: 'templates/tasks/task.html',
	controller: function(){

	},
	controllerAs: 'ctrl',
	bindings: {
		id: '=',
		title: '=',
		start: '=',
		end: '=',
		desc: '='
	}
}

angular
	.module('main')
	.component('task', task);
```

This code allowed me to use a <task> element in my views. The result was similar to the Rails partials from my older projects, but perhaps even more readable and expressive!

```
./public/templates/tasks/index.html

		<li ng-repeat="task in taskCtrl.data | pastDue: onOff | filter: taskSearch">
			<a href="" ui-sref="ngtask({id: task.id})">
				<task id="task.id" title="task.title", start="task.start" 
					end="task.end" desc="task.description">
				</task>
			</a>
		</li>
		
./public/templates/tasks/show.html
<task id="taskCtrl.data.id" title="taskCtrl.data.title" start="taskCtrl.data.start"
	end="taskCtrl.data.end" desc="taskCtrl.data.description">
</task>

```
I handled all of my routing through angular's ui-router service. This allowed me to have some nested views, such as a new-form view and a calendar view that would both show up on the same page as my tasks index.

```
	])
	.config(function($stateProvider){
		$stateProvider
			.state('home', {
				url: '/home',
				templateUrl: 'templates/home.html'
			})
			.state('ngtasks', {
				url: '/ngtasks',
				templateUrl: 'templates/tasks/index.html',
				controller: 'TaskController as taskCtrl',
				resolve: {
					task: function($http, $stateParams, Auth){
						return $http.get('/tasks');
					}
				}
			})
			.state('ngtasks.calendar', {
				url: '/ngtasks/calendar',
				templateUrl: 'templates/tasks/calendar.html'
			})
			.state('ngtasks.new', {
				url: '/ngtasks/new',
				templateUrl: 'templates/tasks/new.html'			
			})

```

In my controllers, I made sure to initialize an empty object for any new-post forms. This code helps users avoid submitting any bad data and also resets the form after a submission.

```
./app/assets/javascripts/angular/controllers/TaskController.js

function TaskController(){
  var taskCtrl = this;

  this.newTask = {};
	
  this.addTask = function(){
		$http
			.post('/tasks', { task: taskCtrl.newTask })
			.then(function(res){
				console.log(res.data);
				taskCtrl.data.push(res.data);
			});
		
		taskCtrl.newTask = {};
	};
}
```

I also made a user-authentication breakthrough thanks to a library called angular-devise. I exposed Devise's helper methods in an API, gave my angular app access to it, and was able to create user login, registration, and logout pages, as well as some presentation logic that only shows tasks to the user who created them. For example, my login logic looked like this:

```
./config/application.rb

module TaskulizeMe

    config.to_prepare do
      DeviseController.respond_to :html, :json
    end
		
end

./app/assets/javascripts/angular/controllers/UserController.js

function UserController(user, Auth, $http, $state){
	var userCtrl = this;

	this.data = user.data;

	this.loginParams = {};

	this.signupParams = {};

	this.login = function(){
    var config = {
      headers: {
        'X-HTTP-Method-Override': 'POST'
      }
    };

    Auth.login(userCtrl.loginParams, userCtrl.config).then(function(user) {
      console.log(user); // => {id: 1, ect: '...'}
      $state.go('ngtasks')
    }, function(error) {
        // Authentication failed...
    });

    this.loginParams = {};
	};
}

```

On the presentation side of things, I integrated with the fullcalendar library to allow users to see and click on tasks in a calendar view. Unlike the rest of my app, the library required me to inject $scope into my controllers, and it required me to create an ActiveRecord method for generating a url. 

```
./app/models/Task.rb

class Task < ApplicationRecord
	belongs_to :user
	has_many :notes

	def url
		"/#/ngtasks/" + id.to_s
	end
end

./app/serializers/task_serializer.rb


class TaskSerializer < ActiveModel::Serializer
  attributes :id, :title, :start, :end, :description, :url
  has_one :user, serializer: TaskUserSerializer
  has_many :notes, serializer: TaskNoteSerializer
end

./app/assets/javascripts/controllers/TaskController.js

function TaskController($scope, task, Auth, $http, $state){
	var taskCtrl = this;

	$scope.eventSources = [{
		events: taskCtrl.data,
		color: 'orange',
		textColor: 'black'
	}]
	
}
```
With this code in place, I now have a full-stack Single-Page CRUD app. I plan to continue expanding it to make the most of Angular's organizational and performance benefits. For example, I plan to make even more components, custom directives, and services to DRY up the code. I also plan to add more filters to make the app more customizable for users.


That's all for now, so check out the code on [github](https://github.com/vinvasir/taskulize-me) and stay tuned for further updates!
