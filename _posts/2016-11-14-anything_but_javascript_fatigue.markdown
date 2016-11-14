---
layout: post
title:  "Anything but Javascript fatigue"
date:   2016-11-14 14:12:07 +0000
---

In my early days as a web developer, I was proud to just understand things like callback hell and prototypal inheritance. I felt that browser-based Javascript was easy to pick up but that it quickly presented quirks that seemed hackish to me as a beginner Rubyist. These days, I've continued to absorb many of the Ruby community's preferences (duck-typing and removing ALL conditionals, anyone?). But I've taken a deep dive into the Node ecosystem as well, and found that I love switching gears to it. Below are some of my favorite aspects of it.

**1. Tons of configuration, but it's transparent and easy to replicate**

As a beginner, I loved Rails' convention-over-configuration approach, which helped me focus on my apps' business logic rather than organizational issues. Moreover, Rails' default settings helped me learn general concepts about separation-of-concerns and architectural patterns.

But in the Node ecosystem, I've come to appreciate the flexibility and transparency behind configuring apps. Nodeschool has some decent CLI tutorials for learning how to structure a Javascript module. From there, it's a small step to being able to write your own middleware and easily include it in an Express server. The system may not be as old as Ruby Gems, but I appreciate how accessible it makes things feel.

**2. You can choose the level of abstraction you want**

If the amount of configuration I described doesn't sound great, there's still a good amount of tools available for creating boilerplate code. I've had good experiences with the [create-react-app CLI](https://github.com/facebookincubator/create-react-app), which includes Babel for transpiling ES6 and JSX syntax, and webpack as a convenient built tool. But if you don't want to include all these tools, you can easily use more-minimal installations, straight from Facebook's official documentation or your favorite blogs!

**3. A chance to use the best programming paradigms for the job**

Lately I've been attending a lot of meetups where speakers have discussed the advantages and disadvantages of static typing, functional programming, and test-driven development. With ES6, there may be a big push to making Javascript *look like* a class-based language (it still won't be one under-the-hood). But if you want, you can make the language downright Haskell-like, as Robert Fischer described at Triangle Modern Web. The decision is particularly important if you're using opinionated tools such as Redux or other Flux implementations.

**Where I go from here**

With that said, I've been continuing to develop AngularJS applications, including a new version of my [Comic Book reviews app](https://github.com/vinvasir/cbook-v2/). But I'm also making sure to stay up-to-date with newer, more-component-oriented front end architectures. I'm working through [Pro React](http://www.pro-react.com/) and [Full-stack Redux](http://teropa.info/blog/2015/09/10/full-stack-redux-tutorial.html), with the goal of learning best practices for managing state and actions in the front end. And thanks to Evan Hahn's book *Express in Action*, I've learned how easy Javascript can be to test at all levels. All in all, I'm excited to be a Javascript developer at this time, and love how the ecosystem is truly maturing.