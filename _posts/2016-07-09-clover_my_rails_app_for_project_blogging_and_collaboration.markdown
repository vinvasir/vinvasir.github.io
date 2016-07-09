---
layout: post
title:  "Clover: My Rails App for Project Blogging and Collaboration"
date:   2016-07-09 02:27:37 +0000
---

For my Rails assessment, I created a Content Management System for users to blog about their projects and comment on other users' projects. The app included advanced features such as Facebook Omniauth authentication, class-level scopes, nested routes, and mass assignment through nested forms. I also made use of partials and helpers to make the code as DRY as possible.

I started by setting up the devise gem with its built-in routes and views. As in the Sinatra assessment, I created a navbar using raw html and css. This time, however, I used helper methods for the presentation logic. For example, users would see a sign in link if they were not logged in, and a sign out link if they were. 

```
  def sign_in_or_sign_out
    if user_signed_in?
      link_to "Sign Out", destroy_user_session_path, method: :delete, class: 'right'
    else
      link_to "Sign In", new_user_session_path, class: 'right'
    end
  end
```

With this basic authentication scheme in place, I added a user role enum for members and administrators. I then added the pundit gem to authorize users for different tasks. Regular members would only be able to see their own profiles, while admins would be able to see all user profiles and change other user's roles. 

```
  def index?
    @user.admin?
  end
  
  def show?
    @user.admin? || @user == @record
  end
  
  def edit?
    @user.admin?
  end
```

I then built out the core of my domain model: a table and class for projects. Each project would have a title and a short description blurb. Projects would belong to the user who created them, and would eventually belong to a category as well.

```
  create_table "projects", force: :cascade do |t|
    t.string   "title"
    t.integer  "user_id"
    t.integer  "category_id"
    t.text     "description"
    t.datetime "created_at",                  null: false
    t.datetime "updated_at",                  null: false
    t.boolean  "public",      default: false
  end
```

By including a "public" boolean value in the projects table, I was able to set up more advanced authorization features for projects. Users would be able to decide whether other users could see their projects or not. Admins would still be able to see all projects, but most users would be regular members, only able to see public projects. For the show, edit, and update actions, this feature was particularly easy to implement:

```
  def show?
    @user.admin? ||  @record.user == @user || @record.public
  end
  
  def edit?
    @user.admin? || @record.user == @user
  end
  
  def update?
    @user.admin? || @record.user == @user
  end
```

For the index action, however, I didn't want to create such an all-or-nothing situation. I wanted non-admin users to be able to see alll of the public projects on the index page, so I did not create a pundit authorization for it. Instead, I created a scope in the Project class, which I used in the projects#index controller action.

```
#app/models/project.rb

scope :visible_projects, -> { where(public: true) }

#app/controllers/projects_controller.b

  def index
    if current_user.admin?
      @projects = Project.all
    else
      @projects = Project.visible_projects
    end
  end
```

To make the project class useful, I associated it to a Note model. All users would be allowed to post notes on their own projects, and users could also post comments on other members' public projects. In the Project model, I differentiated between user updates and comments on other posts.

```
  def creator_updates
    self.notes.where(user_id: creator.id)
  end
  
  def comments
    self.notes.where.not(user_id: creator.id)
  end
```

On the view page, I made sure creator_updates were each displayed in their own white box, while comments would be displayed in a less-prominent area at the bottom of the page. 

```

#app/views/projects/show.html.erb

<h3>Updates from the creator:</h3>
<% @project.creator_updates.each do |update| %>
    <div class="display-box">
        <%= render partial: '/notes/note', locals: {note: update, project: @project} %>
    </div>
<% end %>

<br><br>
<h5>Comments from other users:</h5>
<% @project.comments.each do |comment| %>
    <div class="comments-box">
        <%= render partial: '/notes/note', locals: {note: comment, project: @project} %>
    </div>
<% end %>

#app/assets/stylesheets/master.css.scss

.display-box {
  -moz-box-shadow: 1px 1px 2px 0 #d0d0d0;
  -webkit-box-shadow: 1px 1px 2px 0 #d0d0d0;
  box-shadow: 1px 1px 2px 0 #d0d0d0;
  background: #fff;
  border: 1px solid #ccc;
  border-color: #e4e4e4 #bebebd #bebebd #e4e4e4;
  margin-top: 10px;
  margin-bottom: 15px;
  padding: 20px;
}

.comments-box {
    border-style: dotted;
}
```

Both my notes and projects views used partials for the new and edit forms. The notes forms posted to a set of nested routes using RESTful convetions. 

```
#app/views/notes/edit.html.erb

<%= render partial: 'form', locals: {note: @note, project: @project, url: project_note_path(@project, @note)} %>
    #form at /projects/:project_id/notes/:id/edit

#config/routes.rb

  resources :projects do
    resources :notes, only: [:new, :create, :edit, :update]
  end

```

My projects form also included a nested form for categories, which posted to a custom setter method for category_attributes in the my Project model. This instance method ensured that users could not create duplicate categories.

```
  def category_attributes=(attributes)
    self.category = Category.find_or_create_by(name: attributes[:name])
    self.save
    self.category.update(description: attributes[:description]) unless self.category.description
  end
```

With some finishing touches, such as facebook-omniauth and a form to filter the projects index by category, I made sure to stretch my CRUD skills to the max in this project. You can check out the code on [github](http://https://github.com/vinvasir/clover), and stay tuned for further updates!


