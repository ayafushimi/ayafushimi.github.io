---
layout: post
title:  "Making CLI as Ruby Gem"
date:   2016-10-27 21:00:14 +0000
---

## In this article:

* Object Oriented Ruby programming
* Making CLI application
* Scraping website
* Making Ruby Gem
 

## Aim of this project
I'd like to confirm the knowledge about Ruby what I've learned so far.
To be specific:

*  Basics of Ruby
*  How to build CLI
*  How to scrape websites
*  How to deal with RubyGems
 

## Goal of this project
I'm going to make a RubyGem "Recently PS4 Games" which scrapes [this page](https://www.playstation.com/en-us/explore/games/ps4-games/) and displays informations about game.

With this gem, you can see the lists of new and upcoming PS4 games and inquire the details in CLI.

## Making CLI Gem
### 1. Get RubyGem's template

Before starting writing a code, I make a gem template by the command below.

```
bundle gem recently_ps4_games
```

In prompt, there are messages what files are created.

```
create  recently_ps4_games/Gemfile
create  recently_ps4_games/.gitignore
create  recently_ps4_games/lib/recently_ps4_games.rb
create  recently_ps4_games/lib/recently_ps4_games/version.rb
create  recently_ps4_games/recently_ps4_games.gemspec
create  recently_ps4_games/Rakefile
create  recently_ps4_games/README.md
create  recently_ps4_games/bin/console
create  recently_ps4_games/bin/setup
create  recently_ps4_games/.travis.yml
create  recently_ps4_games/.rspec
create  recently_ps4_games/spec/spec_helper.rb
create  recently_ps4_games/spec/recently_ps4_games_spec.rb
create  recently_ps4_games/LICENSE.txt
create  recently_ps4_games/CODE_OF_CONDUCT.md
```

Now I have files what are necessary to create RubyGem.

This time, I'd like to manage my codes in GitHub, so I make new repository in GitHub [here](https://github.com/ayafushimi/recently_ps4_games_cli_gem)

Let's start creating gem.


### 2. Prepare for writing codes

I'd like to make Object Oriented CLI Gem, so I need some files in ```lib``` folder to hold each classes separately.

The classes what I need are below:


| Class Name | Description |
| -------- | -------- |
| Cli | Keep methods responsible for running CLI. |
| Game | Keep methods responsible for dealing with information of games. |
| Scraper | Keep methods responsible for scraping webpages. |


Let's move on making files. I make below files in ```lib``` folder.

```
lib
├─  recently_ps4_games.rb  <-- already exists in template.
└─  recently_ps4_games  <-- already exists in template.
        ├─  cli.rb
        ├─  game.rb 
        ├─  scraper.rb
        └─  version.rb  <-- already exists in template.
```

Because the above three classes' files are in the ```recently_ps4_games``` folder, I need ```RecentlyPs4Games``` module in addition. This module is already defined in ```recently_ps4_games.rb``` file as template so I will use this module.

In ```recenty_ps4_games.rb``` file, the line ```require "recently_ps4_games_cli_gem/version"``` also already exists so I'm going to use this file like config file and add ```require```s here.

At this moment I can add these ```require```s:

```
require_relative "recently_ps4_games/cli.rb"
require_relative "recently_ps4_games/game.rb"
require_relative "recently_ps4_games/scraper.rb"
```

Besides ```lib``` files, I need ```bin``` files to run this gem. I'd like this gem to start with ```recently_ps4_games``` command so I make a file named by this in ```bin``` folder.

Now, ```bin``` folder is like:

```
bin
├─  console  <-- already exists in template.
├─  setup  <-- already exists in template.
└─  recently_ps4_games
```

The ```recently_ps4_games``` file is responsible for running CLI so I need to add lines below to it:

```
#!/usr/bin/env ruby

require_relative '../lib/recently_ps4_games.rb'

RecentlyPs4Games::CLI.new.call  <-- I'm going to define call method as stater of cli
```

As of now, I'm ready to write codes.

### 3. Write a code in /scraper.rb

What I want this ```Scraper``` class to do is:

* Get game list by scraping [this page](https://www.playstation.com/en-us/explore/games/ps4-games/)
* Get game detail by scraping detail pages such as [this page](https://www.playstation.com/en-us/games/rise-of-the-tomb-raider-20-year-celebration-ps4/)

I'd like to use ```open-uri``` and ```nokogiri``` to get HTML data, but the game list page is dynamic webpage so that I can not use ```open-uri``` with this page. So I make the following method instead of using  ```open-uri```.

```
def self.get_dynamic_page_html
  Selenium::WebDriver::PhantomJS.path = Phantomjs.path
  browser = Watir::Browser.new(:phantomjs)
  browser.goto "https://www.playstation.com/en-us/explore/games/ps4-games/"
  browser.html
end
```


> In this method, I use two gems.
> 
> | Gem Name | Homepage |
> | -------- | -------- |
> | watir-webdriver | http://watir.github.io/ |
> | phantomjs | http://www.rubydoc.info/gems/phantomjs/2.1.1.0 |
> 
> 
> And I refer to [this page](http://stackoverflow.com/questions/32379528/selenium-webdriver-and-phantomjs-on-openshift-ruby-error-unable-to-find-phan) to make this method.
> 


Then, I define a ```.scrape_game_list``` method using  this ```.get_dynamic_page_html``` method.

On the other hand, the detail pages are not dynamic so I can make a ```.scrape_details(url)``` method with ```open-uri```.

The code I write in the ```Scraper``` class is:

```
class RecentlyPs4Games::Scraper

  def self.get_dynamic_page_html
    Selenium::WebDriver::PhantomJS.path = Phantomjs.path
    browser = Watir::Browser.new(:phantomjs)
    browser.goto "https://www.playstation.com/en-us/explore/games/ps4-games/"
    browser.html
  end

  def self.scrape_game_list
    games_arr = []
    list_page = Nokogiri::HTML(self.get_dynamic_page_html)
    game_grids = list_page.css("div.inlineTabs.section.gameGrid")
    game_grids.each do |game_grid|
      games = game_grid.css("ul.clearfix li.layout-type-1 div.tile.clearfix div.game-tile-details h2 a.title")
      games.each do |game|
        games_arr << {
          title: game.text,
          detail_url: game.attr("href")
        }
      end
    end
    games_arr
  end

  def self.scrape_details(url)
    detail_page = Nokogiri::HTML(open(url))
    prod_meta = detail_page.css("div.prod-meta")
    release_info = prod_meta.css("ul.release-info li")
    details = {
      discription: prod_meta.css("p.teaser").text,
      release_date: release_info.css("span.releasedate").text.delete("\n\t"),
      genre: release_info[1].children[2].text.delete("\n\t"),
      publisher: release_info[2].children[1].text.delete("\n\t"),
      developer: release_info[3].children[1].text.delete("\n\t")
    }
    details
  end

end
```

### 4. Write a code in /game.rb

The ```Game``` class is in charge of handling games' data. To be specific, it is responsible for to:

* Make ```Game``` instances with some attributes like title and detail_url
* Find specific instance
* Add details as attributes such as description, release_date and so on to instances

Also, this class should be able to take hashes made by the ```Scraper``` class.

To make ```Game``` instances from arrays of hashes of game data, I define two methods, ```#initialize``` and ```.create_by_games_arr```. An ```#initialize``` takes a hash and iterates over keys and values making these into attributes. A ```.create_by_games_arr``` takes an arrays of hashes and iterates over hashes initializing instances. I can make ```Game``` instances from arrays of hashes made by the ```Scraper``` class with a ```.create_by_games_arr(games_arr)``` method.

Go on to the next method. First of all, to find an instance, the ```Game``` class have to keep data of all instances. I can accomplish this by making a class variable ```@@all``` which is an array of ```Game``` instances. An ```#initialize``` is suitable for adding new instance to the ```@@all```. Besides, this finding method will be executed when gem's user chooses which game to inquire so that it needs to take user's choice as an argument. I want to use IDs to choose games by to make inputting easy for user. To do this, ```Game``` instances need to have ID attributes. The ID attributes should be added by an ```#initialize``` method when an instance is initialized. I name this method ```.find_by_id(id)```.

Lastly, to add details to instances as attributes, I make an ```#add_attributes``` method. This method needs to take a hash of game's details' data as argument and iterate over keys and values making these into attributes.

The code I write in the ```Game``` class is:

```
class RecentlyPs4Games::Game
  attr_accessor :id, :title, :detail_url, :discription, :release_date, :genre, :publisher, :developer

  @@all = []

  def initialize(game_hash)
    game_hash.each do |k, v|
      self.send("#{k}=", v)
    end
    @@all << self
    @id = @@all.size
    self
  end

  def self.create_by_games_arr(games_arr)
    games_arr.each do |game|
      self.new(game)
    end
  end

  def self.find_by_id(id)
    self.all.detect do |game|
      game.id == id
    end
  end

  def add_attributes(details_hash)
    details_hash.each do |k, v|
      self.send("#{k}=", v)
    end
    self
  end

  def self.all
    @@all
  end
end
```

### 5. Write a code in /cli.rb

The ```Cli``` class is responsible for running CLI. What this class have to do is:

* Run this gem
* Call a ```Game``` class method to initialize ```Game``` instances
* Display games list and details
* Get input from user

I make a ```#call``` method to run this gem. This method should welcome user, make game instances, interact with user and say bye to user. The first and the last things, welcome user and say bye to user, are only to ```puts``` messages. But the second and the third ones need a bit complex codes so that I make other methods to be nested inside this method.

The first method to be nested is a ```#make_games```. This method calls a ```RecentlyPs4Games::Scraper.scrape_game_list``` to make arrays of hashes of games and a ```RecentlyPs4Games::Game.create_by_games_arr(games_arr)``` to create ```Game``` instances.

The second method, ```#interact_with_user```, has many functions so I make other nested methods again. This method is expected to do five actions, ```#display_list```, ```#ask_what_to_detail```, ```#get_detail```, ```#display_detail``` and ```#ask_exit?```. And the return value of an ```#ask_what_to_detail``` method should be the argument of a ```#get_detail``` method and also the return value of a ```#get_detail``` method should be taken by a ```#display_detail``` method as an argument. Let's see these methods one at a time.

A ```#display_list``` method display all game titles with IDs. It calls a ```RecentlyPs4Games::Game.all``` method to get all ```Game``` instances and iterates over to ```puts``` IDs and titles.

An ```ask_what_to_detail``` method asks gem's user which game to detail, gets input by user and returns the ID as an integer.

A ```#get_detail``` method calls a ```RecentlyPs4Games::Game.find_by_id(id)``` method to find which ```Game``` instance to get details of, a ```RecentlyPs4Games::Scraper.scrape_details(game.detail_url)``` method to scrape detail page and a ```game.add_attributes(detail)``` method to add details as attributes.

A ```#display_detail``` method does just ```puts``` the chosen```Game``` instance's details.

An ```#ask_exit?``` method asks user whether exit this gem or not and returns ```true``` if user inputs "y" or ```false``` if user inputs "n". When this method returns ```false```, an ```#interact_with_user``` method calls again.

Getting these together, the code in the ```Cli``` class is:

```
class RecentlyPs4Games::Cli

  def call
    puts "Welcome to recently_ps4_games!!"
    puts "...now makinging games list..."
    make_games
    interact_with_user
    puts "Bye!"
  end

  def make_games
    games_arr = RecentlyPs4Games::Scraper.scrape_game_list
    RecentlyPs4Games::Game.create_by_games_arr(games_arr)
  end

  def interact_with_user
    display_list
    display_detail(get_detail(ask_what_to_detail))
    interact_with_user unless ask_exit?
  end

  def display_list
    RecentlyPs4Games::Game.all.each do |game|
      puts "#{game.id}. #{game.title}"
    end
  end

  def ask_what_to_detail
    puts "What game would you like to get details?"
    puts "Please input NUMBER."
    input = gets.strip.to_i
    until input.between?(1, RecentlyPs4Games::Game.all.size) do
      puts "Please input valid NUMBER."
      input = gets.strip.to_i
    end
    input
  end

  def get_detail(id)
    game = RecentlyPs4Games::Game.find_by_id(id)
    detail = RecentlyPs4Games::Scraper.scrape_details(game.detail_url)
    game.add_attributes(detail)
  end

  def display_detail(game)
    puts ""
    puts "-------------------------------"
    puts "\e[1m#{game.id}. #{game.title}\e[0m"
    puts ""
    puts "#{game.discription}"
    puts ""
    puts "\e[2mRelease date : \e[0m #{game.release_date}"
    puts "\e[2mGenre        : \e[0m #{game.genre}"
    puts "\e[2mPublisher    : \e[0m #{game.publisher}"
    puts "\e[2mDeveloper    : \e[0m #{game.developer}"
    puts "-------------------------------"
    puts ""
  end

  def ask_exit?
    puts "Do you want to exit? (y/n)"
    input = gets.strip
    until input == "y" || input == "n" do
      puts "Do you want to exit? (y/n)"
      puts "Please input 'y' to exit, 'n' to continue."
      input = gets.strip
    end
    if input == "y"
      true
    elsif input == "n"
      false
    end
  end

end
```

Now, I have almost finished writing codes.

### 6. Write a code in lib/recently_ps4_game.rb

Let's move on to edit a ```lib/recently_ps4_game.rb``` file. I use several new gems in methods so I have to add ```require```s to this file.

The code in this file should be:

```
module RecentlyPs4Games
end

require 'pry'
require 'open-uri'
require 'nokogiri'
require 'watir-webdriver'
require 'phantomjs'

require_relative "recently_ps4_games/cli.rb"
require_relative "recently_ps4_games/game.rb"
require_relative "recently_ps4_games/scraper.rb"
require_relative "recently_ps4_games/version.rb"
```

At last, I have completed codes to run "Recently PS4 Games" gem. Next, let's tidy files up to get ready to publish gem.


### 7. Edit .gemspec

There is a file ```recently_ps4_games.gemspec``` in template. A ```Specification``` class instance is initialized in this file and the informations contained in this instance  will be displayed on rubygems.org like [this](https://rubygems.org/gems/recently_ps4_games).

Referring to [Specification Reference - RubyGems Guides](http://guides.rubygems.org/specification-reference/), I add attributes to this instance like below:

```
Gem::Specification.new do |spec|
  spec.name          = "recently_ps4_games"
  spec.version       = RecentlyPs4Games::VERSION
  spec.authors       = ["ayafushimi"]
  spec.email         = ["aya.fushimi@gmail.com"]

  spec.summary       = %q{Recently PS4 Games}
  spec.description   = %q{With this gem, you can see the lists of new and upcoming PS4 games and inquire the details in CLI.}
  spec.homepage      = "https://github.com/ayafushimi/recently_ps4_games_cli_gem"
  spec.license       = "MIT"

  spec.files         = ["lib/recently_ps4_games.rb", "lib/recently_ps4_games/cli.rb", "lib/recently_ps4_games/game.rb", "lib/recently_ps4_games/scraper.rb", "lib/recently_ps4_games/version.rb"]

  spec.bindir        = "bin"
  spec.executables   << "recently_ps4_games"
  spec.require_paths = ["lib"]

  spec.add_dependency 'nokogiri', "~> 1.6"
  spec.add_dependency 'watir-webdriver', '~> 0.9'
  spec.add_dependency 'phantomjs', '~> 2.1'
  spec.add_development_dependency "bundler", "~> 1.13"
  spec.add_development_dependency "rake", "~> 10.0"
  spec.add_development_dependency "rspec", "~> 3.0"
  spec.add_development_dependency "pry", ">= 0"
end
```


### 8. Package gem

After tidying files up, I make these files into gem package by this command:

```
gem build recently_ps4_games.gemspec
```

The prompt returns:

```
Successfully built RubyGem
Name: recently_ps4_games
Version: 0.1.0
File: recently_ps4_games-0.1.0.gem
```

Now I get my gem "Recently PS4 Games" version 0.1.0. 

> If I have to change the version, I can change it in a ```lib/recently_ps4_games/version.rb``` file.
> 


### 9. publishing gem

Finally, all left to do is publishing my gem.To publish gem, input the command below:

```
gem push recently_ps4_games-0.1.0.gem
```

After inputting RubyGems.org account, this message is shown up:

```
Pushing gem to https://rubygems.org...
Successfully registered gem: recently_ps4_games (0.1.0)
```

At last, my gem is published and I have finished this project.

The gem I make is here: [recently_ps4_games](https://rubygems.org/gems/recently_ps4_games)
