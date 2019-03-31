---
layout: post
title:      "How to Change Your Karma Testing Browser in Angular - For Beginners"
date:       2019-03-31 19:35:09 +0000
permalink:  how_to_change_your_karma_testing_browser_in_angular_-_for_beginners
---



A Flatiron student asked me for help the other day on how to change her Karma testing browser in Angular from Chrome to Firefox.

She specifically focused on switching from Chrome to Firefox, but these instructions can be modified to add browser types instead, or replaced with different browsers. Just do a Google search for 'karma <browser-name> launcher' and replace `karma-firefox-launcher` with that package name below.

Here are the steps:

1. Go to `package.json` and change the `karma-chrome-launcher` package to `karma-firefox-launcher` - As of the writing of this blog post, the current version is `^1.1.0`. 

2. Re-run `npm install` to unintall the previous launcher and install the new one.

3. In `karma.conf.js` in the `:plugins` key, replace `require("karma-chrome-launcher")` with `require("karma-firefox-launcher")` in the plugins array.

4. Still in `karma.conf.js`, go to the `:browsers` key, and replace `Chrome` with `Firefox` in the browsers array.

Then, run `karma start` and your tests should work in the proper browser!
