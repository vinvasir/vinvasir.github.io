---
layout: post
title:  "First impressions of Evan Hahn's Express in Action"
date:   2016-11-22 14:59:57 +0000
---

One of my best learning experiences in the Ruby ecosystem was when I created a Sinatra app from scratch. The process was certainly slower and more error-prone than using Rails' boilerplate, but I learned several fundamentals. No longer was it a mystery how forms pass data through parameters, or how the backend language dealt with sessions and databases. Completing my Sinatra app really made the Ruby web ecosystem click for me. It was only a matter of time before I tried to reach the same understanding of the JavaScript ecosystem.

I picked up Evan Hahn's *Express In Action* at a bookstore one day, and I was hooked by his deep explanations of the Node ecosystem, middleware, and routing in Express. At times, I was disappointed at the lack of detailed examples, especially in the API section, but overall I found the book to be a solid theoretical explanation. In some ways, it made me feel even more comfortable with the Node ecosystem than with raw Ruby. Here are some things that stuck with me:


**Deep explanation of middleware**

Hahn's book quickly gets to a section discussing Express's core features. He explains that middleware is at the heart of this, and that routes are a specific type of middleware that are only executed under certain conditions. Hahn explains that middleware doesn't have to be specifically made for Express. My impression so far is that many JavaScript libraries that use the module pattern and expose a callback can be plugged in to an Express app as middleware. Although I haven't mastered all the details of middleware, Hahn's theoretical explanation has made them seem more accessible than the Ruby Gems ecosystem.

**Lots of great extras on testing and security, though they lack detail**

Hahn includes several chapters on "Express in Context," to cover features such as databases, testing, and security. The database chapter was just enough to learn how to get MongoDB and Mongoose models working. With my past Sinatra experience, that was enough for me, but beginners will definitely need more practice. The testing and security chapters have been a great survey of how to configure several of the tools available, and the book really does show how easy JavaScript can be to test. That said, there isn't much of a comprehensive walkthrough on testing a non-trivial app. I also would have appreciated at least a brief survey of the front end tools available. The lack of detail may be expected for a book this size, but just know what you're getting into.

**Conclusion**

I found *Express in Action* to be a great way to get acquanited with a new framework after having experience in a similar one such as Sinatra. Hahn's approach of giving deep explanations but less code allowed me to get a sense of the big picture of creating Express apps, and he points to a lot of outside resources for tools that he doesn't discuss in-depth. I now feel ready to make Express applications similar to my Sinatra Comic Book Reviews app, but don't expect *Express in Action* on its own to be an all-inclusive reference. 