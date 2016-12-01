---
layout: post
title:  "Making MVC Sinatra Application"
date:   2016-11-30 11:03:29 -0500
---


## In this article:

* Object Oriented Ruby programming
* MVC(Model/View/Controller) application
* ActiveRecord
* Sinatra(including: database, form, session)


## Aim of this project
I'd like to confirm the knowledge about Ruby what I've learned so far.
To be specific:

*  How to build MVC Application
*  How to use ActiveRecord
*  How to use Sinatra



## Goal of this project
I'm going to make a MVC Application "To Visit List" which keeps countries/cities list you want to visit.

To use this app, you need to sign up first. Then you can add countries/cities where you'd like to visit to your home page, view, edit and delete the list. Others cannot view your list.


## Making CLI Gem

### 1. Preparing for writing codes

Before starting writing application codes, I make `Gemfile`, `Rakefile` and `config/environment.rb`.

The `Gemfile` is necessary to install gems that this app needs at a time.

```
# Gemfile

source 'http://rubygems.org'

gem 'sinatra'
gem 'activerecord', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
gem 'rake'
gem 'require_all'
gem 'sqlite3'
gem 'thin'
gem 'shotgun'
gem 'bcrypt'
gem 'rack-flash3'

group :development do
  gem 'pry'
end
```

The `Rakefile` is necessary to use `rake` which let us make and use original command such as `rake db:migrate`. With this app, I use only 'sinatra/activerecord/rake' command so all I have to write is the 'require' line.

```
# Rakefile

require_relative './config/environment.rb'
require 'sinatra/activerecord/rake'
```

The `config/environment.rb` is to establish connection between my app and the database, setting database's name as "development.sqlite".

```
# config/environment,.rb

ENV['SINATRA_ENV'] ||= "development"

require 'bundler/setup'
Bundler.require(:default, ENV['SINATRA_ENV'])

ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/#{ENV['SINATRA_ENV']}.sqlite"
)

require_all 'app'
```

In addition, I make `app` and `db` folders to keep files responsible for application and database.

For now, the tree is:

```
to_visit_list_app
│
├─  app
│     ├─  models
│     ├─  views
│     └─  controllers
├─  db
├─  config
│     └─  environment.rb
├─  Gemfile
└─  Rakefile
```

### 2. Making migration files in `db/migrate`

In this app, I use three models, `User`, `Country` and `City`.

The attributes of each classes are:

* `User` has `username` and `password`
* `Country` has `name` and `region`
* `City` has `name` and `rank`(stands for desired degree to visit)

The relationships between each classes are:

* `User` has many `Countries`
* `User` has many `Cites` through `Countries`
* `Country` belongs to `User`
* `Country` has many `Cities`
* `Cites` belongs to `Country`
* `Cites` belongs to `User` through `Country`

So tables I need are `users`, `countries`and `cities`.

I make five files as below with `rake db:create_migration NAME=filename` command:

```
# db/migrate/01_create_users.rb

class CreateUsers < ActiveRecord::Migration[5.0]
  def change
    create_table :users do |t|
      t.text :username
      t.text :password_digest
    end
  end
end
```

```
# db/migrate/02_create_countries.rb

class CreateCountries < ActiveRecord::Migration[5.0]
  def change
    create_table :countries do |t|
      t.text :name
      t.text :region
    end
  end
end
```

```
# db/migrate/03_create_cities.rb

class CreateCities < ActiveRecord::Migration[5.0]
  def change
    create_table :cities do |t|
      t.text :name
      t.integer :rank
    end
  end
end
```

```
# db/migrate/04_add_user_to_countries.rb

class AddUserToCountries < ActiveRecord::Migration[5.0]
  def change
    add_column :countries, :user_id, :integer
  end
end
```

```
# db/migrate/05_add_country_to_cities.rb

class AddCountryToCities < ActiveRecord::Migration[5.0]
  def change
    add_column :cities, :country_id, :integer
  end
end
```

### 3. Writing models in `app/models`

I need 3 models corresponding to the tables. Each model's classes should inherit from `ActiveRecord::Base` and have `has_many`/`belongs_to` attributes. In addition, the `User` class needs `has_secure_password` to make password encrypted with `bcrypt` gem. Also I make the `#cities`  method in `User` class to get cities easily.

Then the models are written as below:

```
# app/models/user.rb

class User < ActiveRecord::Base
  has_many :countries
  has_secure_password

  def cities
    cities = []
    self.countries.each do |country|
      country.cities do |city|
        cities << city
      end
    end
    cities
  end

end
```

```
# app/models/country.rb

class Country < ActiveRecord::Base
  belongs_to :user
  has_many :cities
end
```

```
# app/models/city.rb

class City < ActiveRecord::Base
  belongs_to :country
end
```


### 4. Writing veiws in `app/views`

I'm going to divide veiws into four parts, main pages, user pages, country pages and city pages.  Main pages include `layout.erb` and `index.erb`(the top page). User pages include `signup.erb`, `login.erb` and `index.erb`(the user's home page). Both of country pages and city pages include `index.erb`(the list of all contents), `show.erb`(the detail page), `create.erb` and `edit.erb`.


### 4-1-a. Writing `layout.erb` in `/vews`

In `layout.erb`, I can write the common parts for all pages such as `<head>` and `<nav>`. I use [Bootstrap](http://getbootstrap.com/)'s CDN as a css file so I need a `<link>` to this in `<head>` tag. In addition, I'd like to change the contents of `<nav>` depending on the log-in status so I write two versions using `if logged_in? ... else`. Then put `yield` where each page's contents shoud be inserted.

```
# app/views/layout.erb

<!doctype html>
<html>
  <head>
    <title>To Visit List</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
  </head>

  <body>
    <nav>
      <a class="navbar-brand" href="/">To Visit List</a>

      <% if logged_in? %>
        <div class="container">
          <p class="navbar-text">Signed in as <%=current_user.username%></p>
          <a href="/logout" class="btn btn-default navbar-btn btn-xs">Log out</a>
        </div>

        <nav class="navbar navbar-default">
          <ul class="nav navbar-nav">
            <li role="presentation" class=<%=@home_page%>><a href="/user">Home</a></li>
            <li role="presentation" class=<%=@countries_page%>><a href="/countries">Countries</a></li>
            <li role="presentation" class=<%=@cities_page%>><a href="/cities">Cities</a></li>
          </ul>
        </nav>

      <% else %>

        <div class="container">
          <a href="/login" class="btn btn-default navbar-btn btn-xs">Log in</a>
          <a href="/signup" class="btn btn-default navbar-btn btn-xs">Sign up</a>
        </div>

      <%end %>
    </nav>

    <div class="container">
        <%= yield %>
    </div>
  </body>
</html>
```

### 4-1-b. Writing `index.erb` in `/vews`

In `index.erb`, I write a short description of this app and put links to the sign up page and the log in page.

```
# app/views/index.erb

<div class="jumbotron">
  <h1>Welcome to "To Visit List"</h1>
  <p>With this app, you can keep countries and cities where you'd like to visit.</p>

  <a href="/signup" type="button" class="btn btn-primary btn-lg">Sign up</a>
  <em class="text-muted">Or already user?</em> <a href="/login">Log in</a>
</div>
```

### 4-2-a. Writing `signup.erb` in `/vews/users`

This page need forms for 'Username' and 'Password' and the area to display error messages if an user makes input errors. In addition, When an user makes errors, I'd like to show the user which form to re-input and preserve the text which doesn't cause error.

So I use `flash[:error]` by `rack-flash` to keep error messages and error classes and instance variables to preserve user's input.

```
# app/views/users/signup.erb

<h1>Sign Up</h1>

<%if flash.has?(:signup_errors)%>
  <%flash[:signup_errors].each do |err|%>
    <div class="alert alert-danger" role="alert">
      <%=err%>
    </div>
  <%end%>
<%end%>

<form action="/signup" method="post">
  <div class="form-group <%=flash[:name_error]%>">
    <label class="control-label" for="username">Username</label>
    <input class="form-control" type="text" name="username" id="username" value="<%=@exist_name%>">
  </div>

  <div class="form-group <%=flash[:pass_error]%>">
    <label class="control-label" for="password">Password</label>
    <input class="form-control" type="password" name="password" id="password">
  </div>

  <input type="submit" value="Sign up">
</form>
```

### 4-2-b. Writing `login.erb` in `/vews/users`

The log-in page has almost same functions as the sigh-up page.

```
# app/views/users/login.erb

<h1>Log in</h1>

<%if flash.has?(:login_errors)%>
  <%flash[:login_errors].each do |err|%>
    <div class="alert alert-danger" role="alert">
      <%=err%>
    </div>
  <%end%>
<%end%>

<form action="/login" method="post">
  <div class="form-group <%=flash[:name_error]%>">
    <label class="control-label" for="username">Username</label>
    <input class="form-control" type="text" name="username" id="username" value="<%=@input%>">
  </div>

  <div class="form-group <%=flash[:pass_error]%>">
    <label class="control-label" for="password">Password</label>
    <input class="form-control" type="password" name="password" id="password">
  </div>

  <input type="submit" value="Log in">
</form>
```


### 4-2-c. Writing `index.erb` in `/vews/users`

This page will be the user's home page showing the user's name and the countries and cities that the user added. I also set the links to add new country and city.

To show the user's information, `current_user` method works.

```
# app/views/users/index.erb

<h1><%=current_user.username%>'s list</h1>

<a class="btn btn-default" href="/countries/create" role="button">add COUNTRY</a>
<a class="btn btn-default" href="/cities/create" role="button">add CITY</a>

<%current_user.countries.all.each do |country|%>
  <h3><a href="/countries/<%=country.id%>"><%=country.name%></a></h3>
  <table class="table table-hover">
	
    <%country.cities.each do |city|%>
      <tbody class="container">
        <tr class="row">
          <th scope="row" class="col-md-6">
            <a href="/cities/<%=city.id%>"><%=city.name%></a>
          </th>
          <td class="col-md-6">
            <%city.rank.times do%>
              <span class="glyphicon glyphicon-heart" aria-hidden="true"></span>
            <%end%>
            <%(5 - city.rank).times do%>
              <span class="glyphicon glyphicon-heart-empty" aria-hidden="true"></span>
            <%end%>
          </td>
        </tr>
      </tbody>
    <%end%>
		
  </table>
<%end%>
```


### 4-3-a. Writing `index.erb` in `/vews/countries`

This page displays the list of the countries which the user added. In addition, I make badges showing the number of cities the country has.

```
# app/views/countries/index.erb

<h1>Country List</h1>

<a class="btn btn-default" href="/countries/create" role="button">add COUNTRY</a>

<h3>Africa</h3>
  <div class="container">
    <%current_user.countries.all.select{|x| x.region=="Africa"}.each do |country|%>
      <h4><a href="/countries/<%=country.id%>"><%=country.name%></a><span class="badge"><%=country.cities.count%> cities</span></h4>
    <%end%>
  </div>

<h3>Americas</h3>
  <div class="container">
    <%current_user.countries.all.select{|x| x.region=="Americas"}.each do |country|%>
      <h4><a href="/countries/<%=country.id%>"><%=country.name%></a><span class="badge"><%=country.cities.count%> cities</span></h4>
    <%end%>
  </div>

<h3>Asia</h3>
  <div class="container">
    <%current_user.countries.all.select{|x| x.region=="Asia"}.each do |country|%>
      <h4><a href="/countries/<%=country.id%>"><%=country.name%></a><span class="badge"><%=country.cities.count%> cities</span></h4>
    <%end%>
  </div>

<h3>Europe</h3>
  <div class="container">
    <%current_user.countries.all.select{|x| x.region=="Europe"}.each do |country|%>
      <h4><a href="/countries/<%=country.id%>"><%=country.name%></a><span class="badge"><%=country.cities.count%> cities</span></h4>
    <%end%>
  </div>

<h3>Oceania</h3>
  <div class="container">
    <%current_user.countries.all.select{|x| x.region=="Oceania"}.each do |country|%>
      <h4><a href="/countries/<%=country.id%>"><%=country.name%></a><span class="badge"><%=country.cities.count%> cities</span></h4>
    <%end%>
  </div>
```


### 4-3-b. Writing `show.erb` in `/vews/countries`

This page shows the details of a country such as the region and cities. The cities' rank is displayed as the number of heart marks. Also there are links to add city, edit and delete. In addition, I put a breadcrumb to show where the user is now.

```
# app/views/countries/show.erb

<ol class="breadcrumb">
  <li><a href="/user">Home</a></li>
  <li><a href="/countries">Countries</a></li>
  <li class="active"><%=@country.name%></li>
</ol>

<h1><%=@country.name%></h1>

<a class="btn btn-primary" href="/cities/create" role="button">add CITY</a>
<a class="btn btn-default" href="/countries/<%=@country.id%>/edit" role="button">edit</a>
<a class="btn btn-default" href="/countries/<%=@country.id%>/delete" role="button">delete</a>

<h3>Region: <strong><%=@country.region%></strong></h3>

<h3>Cities</h3>

<table class="table table-hover">
  <%@country.cities.each do |city|%>
    <tbody class="container">
      <tr class="row">
        <th scope="row" class="col-md-6">
          <a href="/cities/<%=city.id%>"><%=city.name%></a>
        </th>
        <td class="col-md-6">
          <%city.rank.times do%>
            <span class="glyphicon glyphicon-heart" aria-hidden="true"></span>
          <%end%>
          <%(5 - city.rank).times do%>
            <span class="glyphicon glyphicon-heart-empty" aria-hidden="true"></span>
          <%end%>
        </td>
      </tr>
    </tbody>
  <%end%>
</table>
```


### 4-3-c. Writing `create.erb` in `/vews/countries`

This page is to create new country having forms for 'Country Name' and 'Region'. The user can select region from options. Optionally, the user can add new city at the same time.

Similarly to the `/users/login.erb`, this page should have error messages and error status and preserve the user's input.

```
# app/views/countries/create.erb

<h1>Add Country</h1>

<%if flash.has?(:create_errors)%>
  <%flash[:create_errors].each do |err|%>
    <div class="alert alert-danger" role="alert">
      <%=err%>
    </div>
  <%end%>
<%end%>

<form action="/countries/create" method="post">
  <div class="form-group <%=flash[:country_name_error]%>">
    <label class="control-label" for="coutry_name">Country Name</label>
    <input class="form-control" type="text" name="country[name]" id="coutry_name" value="<%=@country_name_input%>">
  </div>

  <div class="form-group <%=flash[:country_region_error]%>">
    <label class="control-label" for="region">Region</label>
    <select class="form-control" name="country[region]" id="region">
      <option value="">Select Region</option>
      <%REGIONS.each do |region|%>
        <%var="@target#{region}"%>
        <option value=<%=region%> <%=eval(var)%>><%=region%></option>
      <%end%>
    </select>
  </div>

  <h3>Add City (optional)</h3>

  <div class="form-group  <%=flash[:city_name_error]%>">
    <label class="control-label" for="city_name">City Name</label>
    <input class="form-control" type="text" name="city[name]" id="city_name" value="<%=@city_name_input%>">
  </div>

  <div class="form-group <%=flash[:city_rank_error]%>">
    <label class="control-label" for="rank">Rank (How much do you want to visit this city?)</label>
    <select class="form-control" name="city[rank]" id="rank">
      <option value="">Select Rank</option>
      <%RANKS.each do |rank|%>
        <%var="@target#{rank}"%>
        <option value=<%=rank%> <%=eval(var)%>><%=rank%></option>
      <%end%>
    </select>
  </div>

  <input type="submit" value="Add">
</form>
```


### 4-3-d. Writing `edit.erb` in `/vews/countries`

This page has almost same functions as the create page but not option to create new city.

```
# app/views/countries/edit.erb

<h1>Edit Country</h1>

<%if flash.has?(:edit_errors)%>
  <%flash[:edit_errors].each do |err|%>
    <div class="alert alert-danger" role="alert">
      <%=err%>
    </div>
  <%end%>
<%end%>

<form action="/countries/<%=@country.id%>/edit" method="post">
  <div class="form-group <%=flash[:country_name_error]%>">
    <label class="control-label" for="coutry_name">Country Name</label>
    <input class="form-control" type="text" name="country[name]" id="coutry_name" value="<%=@country_name_input%>">
  </div>

  <div class="form-group <%=flash[:country_region_error]%>">
    <label class="control-label" for="region">Region</label>
    <select class="form-control" name="country[region]" id="region">
      <option value="">Select Region</option>
      <%REGIONS.each do |region|%>
        <%var="@target#{region}"%>
        <option value=<%=region%> <%=eval(var)%>><%=region%></option>
      <%end%>
    </select>
  </div>

  <input type="submit" value="Update">
</form>
```


### 4-4-a. Writing `index.erb` in `/vews/cities`

This page displays the list of the cities and its ranks which the user added.

```
# app/views/cities/index.erb

<h1>City List</h1>

<a class="btn btn-default" href="/cities/create" role="button">add CITY</a>

<%current_user.countries.all.each do |country|%>
  <h3><%=country.name%></h3>
  <div class="container">
    <%country.cities.each do |city|%>
      <h4>
        <a href="/cities/<%=city.id%>"><%=city.name%></a>
        <span class="badge">
          <%city.rank.times do%>
            <span class="glyphicon glyphicon-heart" aria-hidden="true"></span>
          <%end%>
          <%(5 - city.rank).times do%>
            <span class="glyphicon glyphicon-heart-empty" aria-hidden="true"></span>
          <%end%>
        </span>
      </h4>
    <%end%>
  </div>
<%end%>
```


### 4-4-b. Writing `show.erb` in `/vews/cities`

This page shows the details of a city such as the country and rank. Also there are links to edit and delete. In addition, I put a breadcrumb to show where the user is now.

```
# app/views/cities/show.erb

<ol class="breadcrumb">
  <li><a href="/user">Home</a></li>
  <li><a href="/countries">Countries</a></li>
  <li><a href="/countries/<%=@city.country.id%>"><%=@city.country.name%></a></li>
  <li class="active"><%=@city.name%></li>
</ol>

<h1><%=@city.name%></h1>

<a class="btn btn-default" href="/cities/<%=@city.id%>/edit" role="button">edit</a>
<a class="btn btn-default" href="/cities/<%=@city.id%>/delete" role="button">delete</a>

<h3><%=@city.country.name%></h3>

<h3>
  <%@city.rank.times do%>
    <span class="glyphicon glyphicon-heart" aria-hidden="true"></span>
  <%end%>
  <%(5 - @city.rank).times do%>
    <span class="glyphicon glyphicon-heart-empty" aria-hidden="true"></span>
  <%end%>
</h3>
```


### 4-4-c. Writing `create.erb` in `/vews/cities`

This page is to create new city having forms for 'City Name', 'Rank' and 'Country'. The user can select rank and country from options. Optionally, the user can create new country instead of selecting.

Similarly to the `/users/login.erb`, this page should have error messages and error status and preserve the user's input.

```
# app/views/cities/create.erb

<h1>Add City</h1>

<%if flash.has?(:create_errors)%>
  <%flash[:create_errors].each do |err|%>
    <div class="alert alert-danger" role="alert">
      <%=err%>
    </div>
  <%end%>
<%end%>

<form action="/cities/create" method="post">
  <div class="form-group <%=flash[:name_error]%>">
    <label class="control-label" for="name">City Name</label>
    <input class="form-control" type="text" name="name" id="name" value="<%=@name_input%>">
  </div>

  <div class="form-group <%=flash[:rank_error]%>">
    <label class="control-label" for="rank">Rank (How much do you want to visit this city?)</label>
    <select class="form-control" name="rank" id="rank">
      <option value="">Select Rank</option>
      <%RANKS.each do |rank|%>
        <%var="@target#{rank}"%>
        <option value=<%=rank%> <%=eval(var)%>><%=rank%></option>
      <%end%>
    </select>
  </div>

  <div class="form-group  <%=flash[:country_error]%>">
    <label class="control-label" for="country">Country</label>
    <select onchange="document.getElementById('coutry_name').disabled = !(this.value=='create_country');document.getElementById('region').disabled = !(this.value=='create_country');" class="form-control" name="country_id" id="country">
      <option value="">Select Country</option>
      <optgroup label="Africa">
        <%current_user.countries.all.select{|x| x.region=="Africa"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <optgroup label="Americas">
        <%current_user.countries.all.select{|x| x.region=="Americas"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <optgroup label="Asia">
        <%current_user.countries.all.select{|x| x.region=="Asia"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <optgroup label="Europe">
        <%current_user.countries.all.select{|x| x.region=="Europe"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <optgroup label="Oceania">
        <%current_user.countries.all.select{|x| x.region=="Oceania"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <option value="create_country" <%=@create_country%>>Add New Country</option>
    </select>
  </div>

  <div class="form-group <%=flash[:country_name_error]%>">
    <label class="control-label" for="coutry_name">Country Name</label>
    <input class="form-control" type="text" name="country[name]" id="coutry_name" value="<%=@country_name_input%>" <%=@country_name_disabled%>>
  </div>

  <div class="form-group <%=flash[:country_region_error]%>">
    <label class="control-label" for="region">Region</label>
    <select class="form-control" name="country[region]" id="region" <%=@country_region_disabled%>>
      <option value="">Select Region</option>
      <%REGIONS.each do |region|%>
        <%var="@target#{region}"%>
        <option value=<%=region%> <%=eval(var)%>><%=region%></option>
      <%end%>
    </select>
  </div>

  <input type="submit" value="Add">
</form>
```


### 4-4-d. Writing `edit.erb` in `/vews/cities`

This page has almost same functions as the create page.

```
# app/views/cities/edit.erb

<h1>Edit City</h1>

<%if flash.has?(:edit_errors)%>
  <%flash[:edit_errors].each do |err|%>
    <div class="alert alert-danger" role="alert">
      <%=err%>
    </div>
  <%end%>
<%end%>

<form action="/cities/<%=@city.id%>/edit" method="post">
  <div class="form-group <%=flash[:name_error]%>">
    <label class="control-label" for="name">City Name</label>
    <input class="form-control" type="text" name="name" id="name" value="<%=@name_input%>">
  </div>

  <div class="form-group <%=flash[:rank_error]%>">
    <label class="control-label" for="rank">Rank (How much do you want to visit this city?)</label>
    <select class="form-control" name="rank" id="rank">
      <option value="">Select Rank</option>
      <%RANKS.each do |rank|%>
        <%var="@target#{rank}"%>
        <option value=<%=rank%> <%=eval(var)%>><%=rank%></option>
      <%end%>
    </select>
  </div>

  <div class="form-group  <%=flash[:country_error]%>">
    <label class="control-label" for="country">Country</label>
    <select onchange="document.getElementById('coutry_name').disabled = !(this.value=='create_country');document.getElementById('region').disabled = !(this.value=='create_country');" class="form-control" name="country_id" id="country">
      <option value="">Select Country</option>
      <optgroup label="Africa">
        <%current_user.countries.all.select{|x| x.region=="Africa"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <optgroup label="Americas">
        <%current_user.countries.all.select{|x| x.region=="Americas"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <optgroup label="Asia">
        <%current_user.countries.all.select{|x| x.region=="Asia"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <optgroup label="Europe">
        <%current_user.countries.all.select{|x| x.region=="Europe"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <optgroup label="Oceania">
        <%current_user.countries.all.select{|x| x.region=="Oceania"}.each do |country|%>
          <%var="@target#{country.id}"%>
          <option value="<%=country.id%>" <%=eval(var)%>><%=country.name%></option>
        <%end%>
      </optgroup>
      <option value="create_country" <%=@create_country%>>Add New Country</option>
    </select>
  </div>

  <div class="form-group <%=flash[:country_name_error]%>">
    <label class="control-label" for="coutry_name">Country Name</label>
    <input class="form-control" type="text" name="country[name]" id="coutry_name" value="<%=@country_name_input%>" <%=@country_name_disabled%>>
  </div>

  <div class="form-group <%=flash[:country_region_error]%>">
    <label class="control-label" for="region">Region</label>
    <select class="form-control" name="country[region]" id="region" <%=@country_region_disabled%>>
      <option value="">Select Region</option>
      <%REGIONS.each do |region|%>
        <%var="@target#{region}"%>
        <option value=<%=region%> <%=eval(var)%>><%=region%></option>
      <%end%>

    </select>
  </div>

  <input type="submit" value="Update">
</form>
```


### 5. Writing controllers in `app/controllers`

I need 4 controllers, `application_controller` to control top page and common methods, `user_controller` to user pages, `country_controller` to country pages, and `city_controller` to city pages.

I'll use `application_controller` as a main controller and make it inherited from `Sinatra::Base`, then make other controllers inherited from `application_controller`.


### 5-1. Writing `application_controller.rb`

I'll define configure, helper and a route to top page in this controller

In `configure`, I have to change `:views` folder to `app/views`, enable secret sessions and declare using `rack-flash`. In addition, I'd like to define two constants to keep options of region and rank.

```
  configure do
    set :views, 'app/views'.to_sym
    enable :sessions
    set :session_secret, 'visitlist secret'
		use Rack::Flash
    REGIONS = %w{ Africa Americas Asia Europe Oceania }
    RANKS = %w{ 5 4 3 2 1 }		
  end
```

In `helper`, I define the `logged_in?` method and `redirect_to_login` method because I want this app not to allow non-logged-in user to use. Also I define the `current_user` method so that an user can not edit or delete another user's data. In addition, I define `selected_ctler` to reduce the repetition around controlling `selected` attribute.

```
  helpers do
    def logged_in?
      !!session[:user_id]
    end

    def current_user
      User.find(session[:user_id])
    end
		
    def redirect_to_login
      redirect "/login"
    end

    def selected_ctler(arr, target)
      arr.each do |x|
        if x == target
          var = "@target#{x}"
          eval("#{var} = 'selected'")
        end
      end
    end
  end
```

Lastly, I make route to "/", redirecting logged-in user to user's page.

```
  get "/" do
    if logged_in?
      redirect "/user"
    else
      erb :index
    end
  end
```

Then get all together in ApplicationController class:

```
# app/controllers/application_controller.rb

require './config/environment'
require 'rack-flash'

class ApplicationController < Sinatra::Base

  configure do
    ...
  end

  helpers do
    ...
  end

  get "/" do
    ...
  end

end
```


### 5-2. Writing `user_controller.rb`

Here I define 6 routes around user pages, `get "/user"` to get user's home page, `get "/signup"`, `get "/login"`, `get "/logout"`, `post "/signup"` and `post "/login"`.

The `get "/user"` route let logged-in user get `/users/index.erb`. In this page, the 'Home' of the navigation bar should be activated so the value `"active"` is assigned to the instance variable `@home_page`  here.

```
  get "/user" do
    if logged_in?
      @home_page = "active"
      erb :"/users/index"
    else
      redirect_to_login
    end
  end
```

The `get "/signup"` route let non-logged-in user get `/users/signup.erb`, redirecting logged-in user to the user's home page.

```
  get "/signup" do
    if logged_in?
      redirect "/user"
    else
      erb :"/users/signup"
    end
  end
```

The `get "/login"` route does almost same as The `get "/signup"` route.

```
  get "/login" do
    if logged_in?
      redirect "/user"
    else
      erb :"/users/login"
    end
  end
```

The `get "/logout"` route should reset the session and redirect the user to the top page.

```
  get "/logout" do
    session.clear
    redirect "/"
  end
```

The `post "/signup"` route creates new user, makes `session[:user_id]` and redirects the user to the user's home page if there are no errors. The errors occur when any form is empty or same username already exists. If any error occurs, this route makes error massages, preserve the input as an instance variable and show `/users/signup.erb` again.

```
  post "/signup" do
    empty_name = params[:username].empty?
    empty_pass = params[:password].empty?
    exist_name = !!User.all.detect{|x| x.username == params[:username]}

    if !(empty_name||empty_pass||exist_name)
      user = User.create(username: params[:username], password: params[:password])
      session[:user_id] = user.id
      redirect "/user"
    else
      flash.now[:signup_errors] = []
      if empty_name
        flash.now[:signup_errors] << "Please enter 'Username'"
        flash.now[:name_error] = "has-error"
      elsif exist_name
        flash.now[:signup_errors] << "This Username is already used. Please use another one"
        flash.now[:name_error] = "has-error"
      end
      if empty_pass
        flash.now[:signup_errors] << "Please enter 'Password'"
        flash.now[:pass_error] = "has-error"
      end
      @exist_name = params[:username]
      erb :"/users/signup"
    end
  end
```

The `post "/login"` route checks the username and the password, makes `session[:user_id]` and redirects the user to the user's home page if there are no errors. The errors occur when any form is empty, the username doesn't exist or the password doesn't match the database. If any error occurs, this route makes error massages, preserve the input as an instance variable and show `/users/login.erb` again.

```
  post "/login" do
    empty_name = params[:username].empty?
    empty_pass = params[:password].empty?
    invalid_name = !User.all.detect{|x| x.username == params[:username]}

    if !(empty_name||empty_pass||invalid_name)
      user = User.find_by(username: params[:username]).try(:authenticate, params[:password])
      if user
        session[:user_id] = user.id
        redirect "/user"
      else
        flash.now[:login_errors] = ["Incorrect Password"]
        flash.now[:pass_error] = "has-error"
        @input = params[:username]
        erb :"/users/login"
      end
    else
      flash.now[:login_errors] = []
      if empty_name
        flash.now[:login_errors] << "Please enter 'Username'"
        flash.now[:name_error] = "has-error"
      elsif invalid_name
        flash.now[:login_errors] << "Incorrect Username"
        flash.now[:name_error] = "has-error"
      end
      if empty_pass
        flash.now[:login_errors] << "Please enter 'Password'"
        flash.now[:pass_error] = "has-error"
      end
      @input = params[:username]
      erb :"/users/login"
    end
  end
```

Then get the aboves together in `UsersController` class:

```
# app/controllers/user_controller.rb

class UsersController < ApplicationController

  get "/user" do
    ...
  end

  get "/signup" do
    ...
  end

  get "/login" do
    ...
  end

  get "/logout" do
    ...
  end

  post "/signup" do
    ...
  end

  post "/login" do
    ...
  end

end
```


### 5-3. Writing `country_controller.rb`

This controller has 7 routes around country pages, `get "/countries"` to get countries' list page, `get "/countries/create"` to get create form page, `get "/countries/:id"` to get a country's detail page, `get "/countries/:id/edit"`, `get "/countries/:id/delete"`, `post "/countries/create"` and `post "/countries/:id/edit"`.

The `get "/countries"` route let logged-in user get `/countries/index.erb`. In this page, the 'Countries' of the navigation bar should be activated so the value `"active"` is assigned to the instance variable `@countries_page`  here.

```
  get "/countries" do
    if logged_in?
      @countries_page = "active"
      erb :"/countries/index"
    else
      redirect_to_login
    end
  end
```

The `get "/countries/create"` route let logged-in user get `/countries/create.erb`.

```
  get "/countries/create" do
    if logged_in?
      erb :"/countries/create"
    else
      redirect_to_login
    end
  end
```

The `get "/countries/:id"` route let logged-in user get `/countries/show.erb`. To know which country to be showed in this page, this route uses `.find(id)` method with the id in the URL and assigns it to the instance variable `@country`. Also to prevent users from seeing other's page, only owner can access this page.

```
  get "/countries/:id" do
    if logged_in?
      @country = Country.find(params[:id])
      if @country.user == current_user
        erb :"/countries/show"
      else
        redirect "/user"
      end
    else
      redirect_to_login
    end
  end
```

The `get "/countries/:id/edit"` route let logged-in user get `/countries/edit.erb`. This route defines 3 instance variables to know which country instance to edit and make the forms have default values.

```
  get "/countries/:id/edit" do
    if logged_in?
      @country = Country.find(params[:id])
      if @country.user == current_user
        @country_name_input = @country.name
        selected_ctler(REGIONS, @country.region)
        erb :"/countries/edit"
      else
        redirect "/user"
      end
    else
      redirect_to_login
    end
  end
```

The `get "/countries/:id/delete"` route delete the country instance with its cities and redirect the user to "/countries".

```
  get "/countries/:id/delete" do
    if logged_in?
      country=Country.find(params[:id])
      if country.user == current_user
        country.cities.each do |city|
          city.delete
        end
        country.delete
        redirect "/countries"
      else
        redirect "/user"
      end
    else
      redirect_to_login
    end
  end
```


The `post "/countries/create"` route creates new country instance and optionally new city instance and redirects the user to the country's detail page if there are no errors. The errors occur when any form is empty or same named country/city already exists. If any error occurs, this route makes error massages, preserves the input as an instance variable and shows `/countries/create.erb` again.

```
  post "/countries/create" do
    empty_country_name = params[:country][:name].empty?
    empty_region = params[:country][:region].empty?
    exist_country = current_user.countries.detect{|x| x.name == params[:country][:name]}
    exist_city = !params[:city][:name].empty? && !!current_user.cities.detect{|x| x.name == params[:city][:name]}
    empty_rank = !params[:city][:name].empty? && params[:city][:rank].empty?

    if !(empty_country_name||empty_region||exist_country||exist_city||empty_rank)
      country = Country.create(params[:country])
      country.user = current_user
      country.save
      if !params[:city][:name].empty?
        city = City.create(params[:city])
        city.country = country
        city.save
      end
      redirect "/countries/#{country.id}"
    else
      flash.now[:create_errors] = []
      if empty_country_name
        flash.now[:create_errors] << "Please enter 'Country Name'"
        flash.now[:country_name_error] = "has-error"
      elsif exist_country
        flash.now[:create_errors] << "This Country already exists. Please check <a href='/countries/#{exist_country.id}' class='alert-link'>this page</a>"
        flash.now[:country_name_error] = "has-error"
      elsif exist_city
        flash.now[:create_errors] << "This City already exists. Please check <a href='/countries/#{City.all.detect{|x| x.name == params[:city][:name]}.id}' class='alert-link'>this page</a>"
        flash.now[:city_name_error] = "has-error"
      end
      if empty_region
        flash.now[:create_errors] << "Please select 'Region'"
        flash.now[:country_region_error] = "has-error"
      end
      if empty_rank
        flash.now[:create_errors] << "Please select 'Rank' of city"
        flash.now[:city_rank_error] = "has-error"
      end
      @country_name_input = params[:country][:name]
      selected_ctler(REGIONS, params[:country][:region])
      @city_name_input = params[:city][:name]
      selected_ctler(RANKS, params[:city][:rank])
      erb :"/countries/create"
    end
  end
```

The `post "/countries/:id/edit"` route updates the country attributes and redirects the user to the country's detail page if there are no errors. The errors occur when any form is empty or same named country/city already exists. If any error occurs, this route makes error massages, preserves the input as an instance variable and shows `/countries/edit.erb` again.

```
  post "/countries/:id/edit" do
    @country = Country.find(params[:id])
    empty_country_name = params[:country][:name].empty?
    empty_region = params[:country][:region].empty?
    exist_country = Country.all.detect{|x| x.name == params[:country][:name]} && params[:country][:name] != @country.name

    if !(empty_country_name||empty_region||exist_country)
      @country.update(params[:country])
      redirect "/countries/#{@country.id}"
    else
      flash.now[:edit_errors] = []
      if empty_country_name
        flash.now[:edit_errors] << "Please enter 'Country Name'"
        flash.now[:country_name_error] = "has-error"
      elsif exist_country
        flash.now[:edit_errors] << "This Country already exists. Please check <a href='/countries/#{exist_country.id}' class='alert-link'>this page</a>"
        flash.now[:country_name_error] = "has-error"
      end
      if empty_region
        flash.now[:edit_errors] << "Please select 'Region'"
        flash.now[:country_region_error] = "has-error"
      end
      @country_name_input = params[:country][:name]
      selected_ctler(REGIONS, params[:country][:region])
      erb :"/countries/edit"
    end
  end
```

Put the aboves together in `CountryController` class:

```
# app/controllers/country_controller.rb

class CountryController < ApplicationController

  get "/countries" do
    ...
  end

  get "/countries/create" do
    ...
  end

  get "/countries/:id" do
    ...
  end

  get "/countries/:id/edit" do
    ...
  end

  get "/countries/:id/delete" do
    ...
  end

  post "/countries/create" do
    ...
  end

  post "/countries/:id/edit" do
    ...
  end

end
```


### 5-4. Writing `city_controller.rb`

This controller has 7 routes around city pages, `get "/cities"` to get cities' list page, `get "/cities/create"` to get create form page, `get "/cities/:id"` to get a city's detail page, `get "/cities/:id/edit"`, `get "/cities/:id/delete"`, `post "/cities/create"` and `post "/cities/:id/edit"`.

The `get "/cities"` route let logged-in user get `/cities/index.erb`. In this page, the 'Cities' of the navigation bar should be activated so the value `"active"` is assigned to the instance variable `@cities_page`  here.

```
  get "/cities" do
    if logged_in?
      @cities_page = "active"
      erb :"/cities/index"
    else
      redirect_to_login
    end
  end
```

The `get "/cities/create"` route let logged-in user get `/cities/create.erb`. Because the 'Country Name' form and the 'Region' form should be 'disabled' before an user select 'Add New Country, two instance variables are defined to control the attributes here.

```
  get "/cities/create" do
    if logged_in?
      @country_name_disabled = "disabled"
      @country_region_disabled = "disabled"
      erb :"/cities/create"
    else
      redirect_to_login
    end
  end
```

The `get "/cities/:id"` route let logged-in user get `/cities/show.erb`. To know which country to be showed in this page, this route uses `.find(id)` method with the id in the URL and assigns it to the instance variable `@city`. Also to prevent users from seeing other's page, only owner can access this page.

```
  get "/cities/:id" do
    if logged_in?
      @city = City.find(params[:id])
      if @city.country.user == current_user
        erb :"/cities/show"
      else
        redirect "/user"
      end
    else
      redirect_to_login
    end
  end
```

The `get "/cities/:id/edit"` route let logged-in user get `/cities/edit.erb`. This route defines 6 instance variables, one to know which city instance to edit, three to make the forms have default values and the others to control the 'disabled' attributes of forms. 

```
  get "/cities/:id/edit" do
    if logged_in?
      @city = City.find(params[:id])
      if @city.country.user == current_user
        @name_input = @city.name
        selected_ctler(RANKS, @city.rank.to_s)
        country_ids = []
        current_user.countries.each {|x| country_ids << x.id}
        selected_ctler(country_ids, @city.country.id)
        @country_name_disabled = "disabled"
        @country_region_disabled = "disabled"
        erb :"/cities/edit"
      else
        redirect "/user"
      end
    else
      redirect_to_login
    end
  end
```

The `get "/cities/:id/delete"` route delete the city instance and redirect the user to "/cities".

```
  get "/cities/:id/delete" do
    if logged_in?
      city = City.find(params[:id])
      if city.country.user == current_user
        city.delete
        redirect "/cities"
      else
        redirect "/user"
      end
    else
      redirect_to_login
    end
  end
```


The `post "/cities/create"` route creates new city instance and optionally new country instance and redirects the user to the city's detail page if there are no errors. The errors occur when any form is empty or same named country/city already exists. If any error occurs, this route makes error massages, preserves the input as an instance variable and shows `/cities/create.erb` again.

```
  post "/cities/create" do
    empty_name = params[:name].empty?
    exist_city = current_user.cities.detect{|x| x.name == params[:name]}
    empty_rank = params[:rank].empty?
    empty_country = params[:country_id].empty?
    create_country = (params[:country_id] == "create_country")
    if create_country
      empty_country_name = params[:country][:name].empty?
      exist_country = current_user.countries.detect{|x| x.name == params[:country][:name]}
      empty_region =  params[:country][:region].empty?
    end

    if !(empty_name||exist_city||empty_rank||empty_country||empty_country_name||exist_country||empty_region)
      city = City.create(name:params[:name], rank:params[:rank])
      if !create_country
        city.update(country_id:params[:country_id])
      else
        country = Country.create(params[:country])
        country.update(user_id:current_user.id)
        city.update(country_id:country.id)
      end
      redirect "/cities/#{city.id}"
    else
      flash.now[:create_errors] = []
      if empty_name
        flash.now[:create_errors] << "Please enter 'City Name'"
        flash.now[:name_error] = "has-error"
      elsif exist_city
        flash.now[:create_errors] << "This City already exists. Please check <a href='/cities/#{exist_city.id}' class='alert-link'>this page</a>"
        flash.now[:name_error] = "has-error"
      end
      if empty_rank
        flash.now[:create_errors] << "Please select 'Rank'"
        flash.now[:rank_error] = "has-error"
      end
      if empty_country
        flash.now[:create_errors] << "Please select 'Country'"
        flash.now[:country_error] = "has-error"
      end

      @name_input = params[:name]
      selected_ctler(RANKS, params[:rank])
      country_ids = []
      current_user.countries.each {|x| country_ids << x.id}
      selected_ctler(country_ids, params[:country_id].to_i)
      @country_name_disabled = "disabled"
      @country_region_disabled = "disabled"

      if create_country
        if empty_country_name
          flash.now[:create_errors] << "Please enter 'Country Name'"
          flash.now[:country_name_error] = "has-error"
        elsif exist_country
          flash.now[:create_errors] << "This Country already exists. Please select from the avobe list"
          flash.now[:country_name_error] = "has-error"
        end
        if empty_region
          flash.now[:create_errors] << "Please select 'Region'"
          flash.now[:country_region_error] = "has-error"
        end
        @create_country = "selected"
        @country_name_disabled = ""
        @country_region_disabled = ""
        @country_name_input = params[:country][:name]
        selected_ctler(REGIONS, params[:country][:region])
      end
      erb :"/cities/create"
    end
  end
```

The `post "/cities/:id/edit"` route updates the city attributes and redirects the user to the city's detail page if there are no errors. The errors occur when any form is empty or same named country/city already exists. If any error occurs, this route makes error massages, preserves the input as an instance variable and shows `/cities/edit.erb` again.

```
  post "/cities/:id/edit" do
    @city = City.find(params[:id])

    empty_name = params[:name].empty?
    exist_city = City.all.detect{|x| x.name == params[:name]} && params[:name] != @city.name
    empty_rank = params[:rank].empty?
    empty_country = params[:country_id].empty?
    create_country = (params[:country_id] == "create_country")
    if create_country
      empty_country_name = params[:country][:name].empty?
      exist_country = Country.all.detect{|x| x.name == params[:country][:name]}
      empty_region =  params[:country][:region].empty?
    end

    if !(empty_name||exist_city||empty_rank||empty_country||empty_country_name||exist_country||empty_region)
      @city.update(name:params[:name], rank:params[:rank])
      if !create_country
        @city.update(country_id:params[:country_id])
      else
        country = Country.create(params[:country])
        country.update(user_id:current_user.id)
        @city.update(country_id:country.id)
      end
      redirect "/cities/#{@city.id}"
    else
      flash.now[:edit_errors] = []
      if empty_name
        flash.now[:edit_errors] << "Please enter 'City Name'"
        flash.now[:name_error] = "has-error"
      elsif exist_city
        flash.now[:edit_errors] << "This City already exists. Please check <a href='/cities/#{exist_city.id}' class='alert-link'>this page</a>"
        flash.now[:name_error] = "has-error"
      end
      if empty_rank
        flash.now[:edit_errors] << "Please select 'Rank'"
        flash.now[:rank_error] = "has-error"
      end
      if empty_country
        flash.now[:edit_errors] << "Please select 'Country'"
        flash.now[:country_error] = "has-error"
      end

      @name_input = params[:name]
      selected_ctler(RANKS, params[:rank])
      country_ids = []
      current_user.countries.each {|x| country_ids << x.id}
      selected_ctler(country_ids, params[:country_id].to_i)
      @country_name_disabled = "disabled"
      @country_region_disabled = "disabled"

      if create_country
        if empty_country_name
          flash.now[:edit_errors] << "Please enter 'Country Name'"
          flash.now[:country_name_error] = "has-error"
        elsif exist_country
          flash.now[:edit_errors] << "This Country already exists. Please select from the avobe list"
          flash.now[:country_name_error] = "has-error"
        end
        if empty_region
          flash.now[:edit_errors] << "Please select 'Region'"
          flash.now[:country_region_error] = "has-error"
        end
        @create_country = "selected"
        @country_name_disabled = ""
        @country_region_disabled = ""
        @country_name_input = params[:country][:name]
        selected_ctler(REGIONS, params[:country][:region])
      end
      erb :"/cities/edit"
    end
  end
```

Put the aboves together in `CityController` class:

```
# app/controllers/city_controller.rb

class CityController < ApplicationController

  get "/cities" do
    ...
  end

  get "/cities/create" do
    ...
  end

  get "/cities/:id" do
    ...
  end

  get "/cities/:id/edit" do
    ...
  end

  get "/cities/:id/delete" do
    ...
  end

  post "/cities/create" do
    ...
  end

  post "/cities/:id/edit" do
    ...
  end

end
```


### 6. Preparing for using rack/shotgun

I use `rack` or `shotgun` to run this app in browser. These gems need `config.ru` file. In this file, I have to write `run ApplicationController` to run my app. Also my app needs not only this controller but `UsersController`, `CountryController` and `CityController`so I need `use` these controllers in front of `run`. In addition, my app also need database so 
I have to check if a database already exist before run this app.

```
# config.ru

require './config/environment'

if ActiveRecord::Migrator.needs_migration?
  raise 'Run `rake db:migrate` first.'
end

use CityController
use CountryController
use UsersController
run ApplicationController
```

### 7. Running my application

Enter these commands in Terminal in this order as below.

1. `bundle install` (install necessary gems)
2. `rake db:migrate` (make database}
3. `shotgun --port=9292` (connect to a local server)

Then access `http://localhost:9292/` with a browser.

