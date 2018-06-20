---
layout: post
title:      "Sinatra App - RPG Character Builder"
date:       2018-06-20 16:33:20 +0000
permalink:  sinatra_app_-_rpg_character_builder
---


My Sinatra portfolio app is an [RPG Character Builder](http://https://github.com/VictoriaMeng/rpg-character-builder) that allows users to save and view details of the video game characters they've created and played. 

Users can sign up for accounts, login to view their saved character details, create, edit, and delete the characters they saved, and associate them with games. 

They can also view a total list of all games that have been created and associated with characters on the site, and the number of characters and users associated with them.

The most important, difficult structural decision I made when developing this app is what sort of associations to have between my Character, Game, and User model class.

Since the main purpose of the app is to allow Users to perform CRUD actions on Characters, it was a simple enough choice to give the User a `:has_many` relationship with the Character class. 

However, it was harder to decide how to associate Users with Games. At first, I wrote a UserGame model class, a join table that would've saved both user_ids and game_ids, but realized shortly into coding my app that a UserGame model was redundant. If I gave the Character model a `:belongs_to` relationship with both the User and Game models, then I could give the User and Game models `:has_many :through` associations with each other, through the Character class. Removing the UserGame model made my code more DRY.

Key takeaways from developing this app:

* Avoid unnecessarily complex associations. There is no need to create redundant model classes if the two models can be associated with an existing `:has_many :through` relationship.

* Avoid calling `Class.all` in your views for security reasons. It makes it easier for hackers to access all the data in your table through your browser. It is better to set assign `Class.all` to an instance variable in your controller routes, and invoke the variable in your view.

* When using the `rack-flash3` Ruby gem, `use Rack::Flash` must be invoked in the application_controller after enabling sessions, not in the `config.ru` file. I believe the reason for this is because Rack::Flash depends on having sessions enabled, so if Rack::Flash is loaded by `config.ru` before sessions is enabled, your app will throw an error.
