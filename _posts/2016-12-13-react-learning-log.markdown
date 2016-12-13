---
layout: post
title:  "My progress in learning the React ecosystem"
date:   2016-12-13 14:56:17 +0000
---

As part of my strategy to avoid [Javascript fatigue](http://thinkeringalong.com/2016/11/14/anything_but_javascript_fatigue/), I've been focusing on learning a small number of new frameworks so that I can focus more on learning their [architectural concepts](http://thinkeringalong.com/2016/11/29/learning_functional_programming_for_the_front_end/). Based on where the front end market is going, I've decided to focus on learning functional programming for state management and component-based patterns for the view layer. To get there, I've been working through tutorials on React and Redux, while occasionally glancing at the VueJS documentation to see what a lighter-weight version of these design patterns can look like. Here's some highlights so far:

* **Learned how to use the Javascript Immutable module from the Full-stack Redux tutorial**: Although this [web tutorial](http://teropa.info/blog/2015/09/10/full-stack-redux-tutorial.html) doesn't always have the most thorough explanations for React itself, it introduces a lot of great coding practices to make sure that your reducer functions don't have side effects. I especially like the unit-test-driven approach that helps you write functions with well-encapsulated logic, which you can then pass into a reducer later on.
* **Working through the Full-stack React ebook and code examples**: I've found this series to have particularly good explanations of how the different layers of an application fit together. Even when it explains a subject in isolation, such as React components, this book makes sure that you start out doing it the "React way." For example, they always encourage you to maintain one-way data flow and minimize the amount of state in components. As a result, when the book gets around to Redux, it only takes a few small changes to make the new concepts fit into apps from earlier chapters.
