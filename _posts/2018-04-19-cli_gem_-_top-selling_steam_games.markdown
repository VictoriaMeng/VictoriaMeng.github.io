---
layout: post
title:      "CLI Gem - Top-Selling Steam Games"
date:       2018-04-19 19:14:24 +0000
permalink:  cli_gem_-_top-selling_steam_games
---


I love video games and I love Steam, so I decided to build a CLI Gem around Steam.

My `game_gem` scrapes the Top-Selling list of games on the [Steam homepage](http://store.steampowered.com/) and prints out a list of the top 30 best-selling games.

Once the list is printed out, users may enter a number between 1-30 to look at a game's final price after discounts.

Then, they may go back to the main menu to select another game from the list, or quit the app.


## Code Structure

My gem contains three classes: A `Game` class that instantiates each game with a `@name` and `@price`, a `GameScraper` class which scrapes the top-selling data from Steam and saves it into a hash, and a `GameCLI` class which controls the gem's UI.

When planning out the code structure, I always knew that there would be a `GameCLI` and `Game` class. 

A `GameCLI` class is obviously needed to provide a UI, while a `Game` class would allow me to encapsulate each game's data into it's own instance. If I ever expand on the app to show more details besides the final price, like genre or discount %, it would be fairly straightforward to save those new details in `Game` instance variables like `@genre`. Since my app needs to save and print the details of at least 30 games, having a class to handle each game is essential.

That most difficult structural decision was whether to include a `GameScraper` class, a `GameScraper` module, or to simply code the scraper methods into the other two classes.

I decided to include a `GameScraper` class for two reasons: 1st, it leads to cleaner encapsulation if the `GameCLI` only controls UI interaction methods and the `Game` class only instantiates games. 2nd, having `GameScraper` as a class allows me to easily save other HTML data containing scraped lists of games into `GameScraper` instance variables if I ever expand on this app. I could refactor my current `@games_html` instance variable into `@top_sellers`, and then save the other scraped lists into `@new_and_trending` or `@most_played` as needed.  


