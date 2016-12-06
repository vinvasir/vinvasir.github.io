---
layout: post
title:  "Changing Rails' default settings to use Postgres"
date:   2016-12-05 19:34:33 +0000
---

This past week I returned to some of my old Rails apps to bring them more in-line with some best practices. My first step was to switch from SQLite, the default database in Rails and Django projects, to Postgres. As fun as it was to use a simple, "batteries-included" file for my data persistence, I wanted to switch to a more widely used database that I could use for deploying to Heroku. On interviews and open source projects, I've almost exclusively used Postgres used in Rails and Django apps, so I decided to set it up from scratch in my old [Clover](http://thinkeringalong.com/2016/08/08/clover_now_with_a_jquery_front-end/) project. 

Postgres runs on a database server that has to authenticate apps connected to it. As a result, I had to configure a Postgres superuser on my locally running database server. I used a command found in the documentation [here](https://www.postgresql.org/docs/9.2/static/app-createuser.html) to create a new user as a superuser who could make and maintain databases. 

```
createuser -P -s -e joe

```

This command creates a superuser named "joe" and lets you set up a password immediately.

Afterwards, I removed the default 'sqlite3' gem from my Rails Gemfile and replaced it with the gem 'pg'. Then, I changed the database.yml file to have the following default settings:

```
default: &default
  adapter: postgresql
  host: localhost
  username: localuser
  password: local

```

To tie it all together, I made my development and test environments use the default settings, and I made the production version use Heroku's environment variables:

```
development:
  <<: *default
  database: task_dev

test:
  <<: *default
  database: task_test

production:
  adapter: postgresql
  host: ENV['DATABASE_URL']
  username: ENV['DATABASE_USERNAME']
  password: ENV['DATABASE_PASSWORD']
```

With all of this set up, I was able to deploy Clover live, and you can find it [here](https://still-woodland-16151.herokuapp.com/)!