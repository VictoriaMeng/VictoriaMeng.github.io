---
layout: post
title:      "Rails and Javascript Portfolio Project - RateMyFav 2.0"
date:       2018-09-29 02:23:18 +0000
permalink:  rails_and_javascript_portfolio_project_-_ratemyfav_2_0
---


Before updating my Rails portfolio project with Javascript functionality, I installed the [jquery-readyselector](https://github.com/Verba/jquery-readyselector) Ruby gem. This gem extends the jquery `.ready()` function so it can be called on jquery selectors (ie., ` i$("#franchise-index").ready` instead of the entire `document`. The `jquery-readyselector` allowed me to split my Javascript into page-specific files while still concatenating them in the Javascript asset pipeline.

Now, when users go to the Franchise index page, clicking on the individual Franchise links will append individual Franchise details to the DOM if they aren't already rendered. These details include a link to the Franchise's show page, where users can click on navigation arrows to view different Franchises and create/update new Ratings for those Franchises.

Each time the app submits an AJAX request to retrieve data to append to the DOM, the data is converted into a Franchise and/or Rating Javascript prototype object before the object attributes are appended to the DOM.

One bug I learned to watch out for is when data from a JSON object is only appended to the DOM properly when in the Javascript `debugger`, but not when the code is running normally. 

This bug occurs because of the asynchronous nature of Javascript. It means that you have code which depends on data from an AJAX request, but that code is invoked before the AJAX request returns the data you need. The reason why the code only works in `debugger` is because `debugger` pauses the app, giving the time for the AJAX request to complete.

Besides using `async/await`, one easy way to fix the bug is to move the dependent code into the AJAX request itself, so the code is only invoked once the data is retreived.
