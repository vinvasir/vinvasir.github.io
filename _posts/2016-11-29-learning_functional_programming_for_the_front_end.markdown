---
layout: post
title:  "Learning functional programming for the front end"
date:   2016-11-29 13:19:17 +0000
---

One of my goals as a continuous learner has been to focus on concepts and design patterns rather than the syntactical quirks of frameworks. Sometime's that's easier said than done when this is [how it feels to learn Javascript in 2016](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f#.5czstptl3). But even with this explosion in front end tools, I've narrowed down a study plan for the current architectural patterns on the front end. Here are some highlights:

**Pure components as a view layer**

From where I'm standing, the trend has been to move away from tightly controlled directives like in Angular 1, towards "pure" components that mostly serve as a dynamic way to display data. These components will still have "actions" so that users can interact with them, but the components themselves will no longer manage business logic. Instead that job goes to:

**Using functional programming to manage state**

Here's the big paradigm shift tying this all together. As developers, we no longer want front-end state to be mutated directly like our backend databases. On the front end, that pattern creates too much unpredictability and makes bugs hard to track down. Instead, today's popular libraries use a data "store" that returns the entire state of the application. This store responds to "actions" from components and then creates an *entirely new state* in response. Since we aim to use pure components anyway, they should re-render whenever the state is replaced by a new one. To me, this approach requires a tigher, more-linear mindset than the back end ORMs I'm used to. For example, all the functions that the store uses have to be pure functions that have a single return value and no side effects.

**Selecting the most-readable libraries to learn these concepts**

Thankfully, I've found that both Redux and Vuex provide highly readable syntax for stores, actions, and dispatchers. The Vue ecosystem certainly has the edge in lightweight-ness, but I'm focusing first on React and Redux tutorials because they have a greater amount of resources. Still, Vue and Vuex's official documentation is already great, so I often find myself cross-referencing them when the Redux implementation of a concept seems clunky or hard to understand. At the end of the day, I find that these state-management libraries are doing similar things, so a comparative approach has helped me let the fundamentals sink in.