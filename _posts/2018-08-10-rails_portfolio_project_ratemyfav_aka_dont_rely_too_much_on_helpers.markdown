---
layout: post
title:      "Rails Portfolio Project: RateMyFav (aka Don't Rely Too Much on Helpers)"
date:       2018-08-10 04:29:47 +0000
permalink:  rails_portfolio_project_ratemyfav_aka_dont_rely_too_much_on_helpers
---


My Ruby on Rails portfolio project is called RateMyFav. It lets users sign up, submit, and vote on their favorite franchises (video games, tv shows, etc.)

The app contains three class models: `User`, `Franchise`, and `Rating`. The `Rating` class model serves a join table, with a `franchise id` and `user_id` attributes. This way, each rating belongs to one user and one franchise. Users and franchises have many instances of each other through the `:ratings` database.

User security is managed by the `bcrypt` gem, which provides methods to automatically salt and authenticate all submitted passwords. There is also an option to login through Facebook via the `oath` gem.

Observations I had when developing the app:

* Don't rely too much on framework-specific helpers. They vary from framework to framework anyways. If you can't get a `form_tag` or `form_for` to work, there's no shame in sticking with classic HTML. A Flatiron Tech Coach told me this. This is especially true if you're writing a form that will not be create or update any database objects, since the `form_tag` and `form_for` methods seem to work best when passing in database objects.

* Partials and locals are your friend. It's so refreshing not to rewrite the same form over and over again. 

* Be careful when writing validations that check if your `params[:id]` is equal to `@my_object.id.` The ids in your database will be integers, while the ids from your params will be strings, guaranteeing a false negative each time. To avoid this bug, call `.to_i` or `.to_s` when necessary. 

