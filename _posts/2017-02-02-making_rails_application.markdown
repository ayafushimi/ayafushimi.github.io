---
layout: post
title:  "Making Rails Application"
date:   2017-02-02 22:25:40 +0000
---


## In this article:

* Ruby on Rails
* MVC(Model/View/Controller) application
* Search box
* Multi-Provider Authentication


## Aim of this project
I'd like to confirm the knowledge about Ruby what I've learned so far.
To be specific:

*  How to build Rails Application
*  How to Multi-Provider Authenticate
*  How to use Nested Forms
*  How to make search box


## Goal of this project
I'm going to make a Rails Application "Recipe Manager". In this app, you can;

* Search recipes by rate, cooking time, ingredients or creator
* Make reports of a recipe as a user
* Make recipes as a creator

Everyone can use this app but you need sign_up to make reports or recipes.


## Making Rails Application

### 1. Preparing for writing codes

First of all, I create new Rails Project with command `rails new recipe_manager`. Now I have all files and folders I need to start coding as below.

```
recipe_manager
├─  app
├─  bin
├─  config
├─  db
├─  lib
├─  log
├─  public
├─  test
├─  tmp
├─  vender
├─  .gitignore
├─  config.ru
├─  Gemfile
├─  Gemfile.lock
├─  Rakefile
└─  README.md
```

### 2. Organizing Model Requirements

This app needs 6 models, `Recipe`, `Ingredient`, `RecipeIngredient`, `Direction`, `Report` and `User`. Let me see details of each models.

#### **`Recipe`**

Associations

* has many `recipe_ingredients`.
* has many `ingredients` through `recipe_ingredients`.
* has many `directions`.
* has many `reports` by user.
* belongs to `creator` (class name: `User`)

Attributes

* title
* cooking time


#### **`Ingredient`**

Associations

* has many `recipe_ingredients`.
* has many `recipes` through `recipe_ingredients`.

Attributes

* title


#### **`RecipeIngredient`**

Associations

* belongs to `recipe`.
* belongs to `ingredient`.

Attributes

* quantity
* unit


#### **`Direction`**

Associations

* belongs to `recipe`.

Attributes

* text of each steps


#### **`Report`**

Associations

* belongs to `recipe`.
* belongs to `user`.

Attributes

* rate
* comment(optional)


#### **`User`**

Associations

* has many `reports`.
* has many `recipes` as creator.

Attributes

* username
* email
* password
* uid (for OAuth)


### 3. Organizing View Requirements

Next, I have to decide what views are necessary for these 6 models. In addition, there should be views for sign-in/sign-out and layout. I also need to consider what links should be included in each views. 


#### **`recipes`**

**index.html.erb (`/recipes`)**

* link to `/recipes/:id`
* link to `/recipes/new`
* link to `/search`

**new.html.erb (`/recipes/new`) (needs log-in)**

* form for `Recipe`
* form for `RecipeIngredient`
* form for `Ingredient`
* form for `Direction`
* link to `[POST]/recipes`

**show.html.erb (`/recipes/:id`)**

* link to creator's page, `/users/:id`
* link to reports' index, `/recipes/:recipe_id/reports`
* link to new report form, `/recipes/:recipe_id/reports/new`
* link to `/recipes/:id/edit` (if owner)
* link to `[DELETE]/recipes/:id` (if owner)

**edit.html.erb (`/recipes/:id/edit`) (needs to be owner)**

* form for `Recipe`
* form for `RecipeIngredient`
* form for `Ingredient`
* form for `Direction`
* link to `[PATCH]/recipes/:id`


#### **`Ingredient`**

No need for views.


#### **`RecipeIngredient`**

No need for views.


#### **`Direction`**

No need for views.


#### **`reports`**

**index.html.erb (`/recipes/:recipe_id/reports`)**

* link to `/recipes/:recipe_id/reports/new`

**new.html.erb (`/recipes/:recipe_id/reports/new`) (needs log-in)**

* form for `Report`
* link to `[POST]/recipes/:recipe_id/reports`

**edit.html.erb (`/reports/:id/edit`) (needs to be owner)**

* form for `Report`
* link to `[PATCH]/reports/:id`


#### **`users`**

**new.html.erb (`/signup`)**

* form for `User`
* link to `[POST]/users`

**show.html.erb (`/users/:id`)**

* link to recipes, `/recipes/:id`
* link to edit recipe form, `/recipes/:id/edit` (if owner)
* link to delete recipe, `[DELETE]/recipes/:id` (if owner)
* link to edit report form, `/recipes/:recipe_id/reports/:id/edit` (if owner)
* link to delete report, `[DLETE]/recipes/:recipe_id/reports/:id` (if owner)
* link to `/users/:id/edit` (if owner)
* link to `[DELETE]/users/:id` (if owner)

**edit.html.erb (`/users/:id/edit`) (needs to be owner)**

* form for `User`
* link to `[PATCH]/users/:id`


#### **`sessions`**

**new.html.erb (`/signin`)**

* link to `[POST]/sessions`


#### **`layouts`**

**application.html.erb**

* link to recipes' index as HOME, `/`
* link to `/signin` (if not logged in)
* link to `/signup` (if not logged in)
* link to `/signout` (if logged in)
* link to his/her page as MyPage, `/users/:id` (if logged in)


### 4. Organizing Controller Requirements

I'm going to think about the controllers' requirements for each views.



#### **`recipes_controller`**

**index**

* defines `@recipes`
* renders `recipes/index.html.erb`

**new**

* creates new `Recipe` as `@recipe`
* renders `recipes/new.html.erb`

**create**

* validates contents of the create form
* creates new `Recipe`
* creates new `RecipeIngredient`
* creates or finds `Ingredient`
* creates new `Direction`
* redirects to `/recipes/:id`

**show**

* defines `@recipe`
* renders `recipes/show.html.erb`

**edit**

* defines `@recipe`
* renders `recipes/edit.html.erb`

**update**

* defines `@recipe`
* validates contents of the edit form
* updates `Recipe`
* updates `RecipeIngredient`
* updates `Ingredient`
* updates `Direction`
* redirects to `/recipes/:id`

**destroy**

* delete `Recipe`
* redirects to `/recipes` 


#### **`reports_controller`**

**index**

* defines `@reports`
* renders `reports/index.html.erb`

**new**

* creates new `Report` as `@report`
* renders `reports/new.html.erb`

**create**

* validates contents of the create form
* creates new `Report`
* redirects to `/recipes/:recipe_id/reports/:id`

**edit**

* defines `@report`
* renders `reports/edit.html.erb`

**update**

* defines `@report`
* validates contents of the edit form
* updates `Report`
* redirects to `/reports/:id`

**destroy**

* delete `Report`
* redirects to `/recipes/:recipe_id/reports` 


#### **`users_controller`**

**new**

* creates new `User` as `@user`
* renders `users/new.html.erb`

**create**

* validates contents of the create form
* creates new `User`
* redirects to `/recipes`

**show**

* defines `@user`
* renders `users/show.html.erb`

**edit**

* defines `@user`
* renders `users/edit.html.erb`

**update**

* defines `@user`
* validates contents of the edit form
* updates `User`
* redirects to `/users/:id`

**destroy**

* delete `User` and his/her `Report`
* redirects to `/recipes`


#### **`sessions_controller`**

**new**

* creates new `User` as `@user`
* renders `sessions/new.html.erb`

**create**

* checks contents of the log-in form against `User`
* defines `session[:user_id]`
* redirects to `/`

**destroy**

* delete `session[:user_id]`
* redirects to `/`


### 5. Generating models, controllers, views and migrations

I use rails generator to make MVC files for above models.

#### **`Recipe`**

The `Recipe` model needs model, controller, view and migration files. I can use `rails generate model` to generate model and migration files and `rails generate controller` to controller and view files. 

The command to generate model is;

```
rails g model Recipe user:belongs_to title:string time:integer link:text
```

Then I get these files:

```
# db/migrate/20170111213209_create_recipes.rb

class CreateRecipes < ActiveRecord::Migration[5.0]
  def change
    create_table :recipes do |t|
      t.belongs_to :user, foreign_key: true
      t.string :title
      t.integer :time
      t.text :link

      t.timestamps
    end
  end
end
```

```
# app/models/recipe.rb

class Recipe < ApplicationRecord
  belongs_to :user
end
```

Let me add associations to `Recipe` class as requirements above.

```
# app/models/recipe.rb

class Recipe < ApplicationRecord
  has many :recipe_ingredients
  has many :ingredients, through: :recipe_ingredients
  has many :directions
  has many :reports
  belongs_to :creator, class_name: "User", foreign_key: "user_id"
end
```

The next command to generate its controller is;

```
rails g controller Recipes index new show edit
```

Now I have these files:

```
# app/controllers/recipes_controller.rb

class RecipesController < ApplicationController
  def index
  end

  def new
  end

  def show
  end

  def edit
  end
end
```

```
# app/views/recipes/edit.html.erb
```

```
# app/views/recipes/index.html.erb
```

```
# app/views/recipes/new.html.erb
```

```
# app/views/recipes/show.html.erb
```

```
# app/helpers/recipes_helper.rb

module RecipesHelper
end
```

```
# config/routes.rb

Rails.application.routes.draw do
  ...
  get 'recipes/index'

  get 'recipes/new'

  get 'recipes/show'

  get 'recipes/edit'
	...
end
```

#### **`Ingredient`**

The `Ingredient` model needs model and migration files.

The command to generate this model is;

```
rails g model Ingredient title:string
```

Then I get these files:

```
# db/migrate/20170111220517_create_ingredients.rb

class CreateIngredients < ActiveRecord::Migration[5.0]
  def change
    create_table :ingredients do |t|
      t.string :title

      t.timestamps
    end
  end
end
```

```
# app/models/ingredient.rb

class Ingredient < ApplicationRecord
end
```

Let me add associations to `Ingredient` class as requirements above.

```
# app/models/ingredient.rb

class Ingredient < ApplicationRecord
  has_many :recipe_ingredients
  has_many :recipes, through: :recipe_ingredients
end
```


#### **`RecipeIngredient`**

The `RecipeIngredient` model needs model and migration files.

The command to generate this model is;

```
rails g model RecipeIngredient recipe:belongs_to ingredient:belongs_to quantity:integer unit:string
```

Then I get these files:

```
# db/migrate/20170111221219_create_recipe_ingredients.rb

class CreateRecipeIngredients < ActiveRecord::Migration[5.0]
  def change
    create_table :recipe_ingredients do |t|
      t.belongs_to :recipe, foreign_key: true
      t.belongs_to :ingredient, foreign_key: true
      t.integer :quantity
      t.string :unit

      t.timestamps
    end
  end
end
```

```
# app/models/recipe_ingredient.rb

class RecipeIngredient < ApplicationRecord
  belongs_to :recipe
  belongs_to :ingredient
end
```

#### **`Direction`**

The `Direction` model needs model and migration files.

The command to generate this model is;

```
rails g model Direction recipe:belongs_to text:text
```

Then I get these files:

```
# db/migrate/20170111221849_create_directions.rb

class CreateDirections < ActiveRecord::Migration[5.0]
  def change
    create_table :directions do |t|
      t.belongs_to :recipe, foreign_key: true
      t.text :text

      t.timestamps
    end
  end
end
```

```
# app/models/direction.rb

class Direction < ApplicationRecord
  belongs_to :recipe
end
```

#### **`Report`**

The `Report` model needs model, controller, view and migration files.

The command to generate model is;

```
rails g model Report recipe:belongs_to user:belongs_to rate:integer comment:text
```

Then I get these files:

```
# db/migrate/20170111222417_create_reports.rb

class CreateReports < ActiveRecord::Migration[5.0]
  def change
    create_table :reports do |t|
      t.belongs_to :recipe, foreign_key: true
      t.belongs_to :user, foreign_key: true
      t.integer :rate
      t.text :comment

      t.timestamps
    end
  end
end
```

```
# app/models/report.rb

class Report < ApplicationRecord
  belongs_to :recipe
  belongs_to :user
end
```

The next command to generate its controller is;

```
rails g controller Reports index new edit
```

Now I have these files:

```
# app/controllers/reports_controller.rb

class ReportsController < ApplicationController
  def index
  end

  def new
  end

  def edit
  end
end
```

```
# app/views/reports/edit.html.erb
```

```
# app/views/reports/index.html.erb
```

```
# app/views/reports/new.html.erb
```

```
# app/helpers/reports_helper.rb

module ReportsHelper
end
```

```
# config/routes.rb

Rails.application.routes.draw do
  ...
  get 'reports/index'

  get 'reports/new'
	
  get 'reports/edit'
	...
end
```

#### **`User`**

The `User` model needs model, controller, view and migration files.

The command to generate model is;

```
rails g model User username:string email:string password:digest uid:integer
```

Then I get these files:

```
# db/migrate/20170111223534_create_users.rb

class CreateUsers < ActiveRecord::Migration[5.0]
  def change
    create_table :users do |t|
      t.string :username
      t.string :email
      t.string :password_digest
			t.integer :uid

      t.timestamps
    end
  end
end
```

```
# app/models/user.rb

class User < ApplicationRecord
  has_secure_password
end
```

Let me add associations to `User` class as requirements above.

```
# app/models/user.rb

class User < ApplicationRecord
  has_many :reports
  has_many :recipes
  has_secure_password
end
```

In addition, to activate `has_secure_password`, I have to add the below line to `Gemfile`.

```
# Gemfile

...
gem 'bcrypt', '~> 3.1.7'
...
```

The next command to generate its controller is;

```
rails g controller Users new show edit
```

Now I have these files:

```
# app/controllers/users_controller.rb

class UsersController < ApplicationController
  def new
  end

  def show
  end

  def edit
  end
end
```

```
# app/views/users/edit.html.erb
```

```
# app/views/users/new.html.erb
```

```
# app/views/users/show.html.erb
```

```
# app/helpers/users_helper.rb

module UsersHelper
end
```

```
# config/routes.rb

Rails.application.routes.draw do
  ...
  get 'users/new'

  get 'users/show'

  get 'users/edit'
	...
end
```

#### **`sessions`**

This app needs sessions' controller and view files.

The command to generate this controller is;

```
rails g controller Sessions new
```

Now I have these files:

```
# app/controllers/sessions_controller.rb

class SessionsController < ApplicationController
  def new
  end
end
```

```
# app/views/sessions/new.html.erb
```

```
# app/helpers/sessions_helper.rb

module SessionsHelper
end
```

```
# config/routes.rb

Rails.application.routes.draw do
  ...
  get 'sessions/new'
	...
end
```

### 6. Writing Routes

There are already some routes in `config/routes.rb` but I'd like to use `resources:` to define routes and set `recipes#index` as `root`.

The points of these routes are;

* `reports`' `#index`, `#new` and `#create` are nested in `recipes`
* `/signup` is linked to `users#new`,  `/signin` to `sessions#new`, `/signout` to `sessions#destroy`

```
# config/routes.rb

Rails.application.routes.draw do
  root 'recipes#index'

  resources :recipes do
    resources :reports, shallow: true
  end

  resources :users, only: [:create, :show, :edit, :update, :destroy]
  get 'signup', to: 'users#new'

  get 'signin', to: 'sessions#new'
  post 'signin', to: 'sessions#create'
  get 'signout', to: 'sessions#destroy'

  get '/auth/google_oauth2/callback', to: 'sessions#oauth'
  get '/auth/facebook/callback', to: 'sessions#oauth'
end

```


### 7. Writing Models

Let me add attributes-writers, validates and methods to each models.


#### **`Recipe`**

This model need to be able to access `RecipeIngredient` and `Direction` because these models will be created and updated at `/recipes/new` or `/recipes/edit`. So I need to add `accepts_nested_attributes_for` to this model. In addition, before deleting recipe instance, its children should be deleted by `before_destroy`.

```
# app/models/recipe.rb

class Recipe < ApplicationRecord
  ...

  validates :title, presence: true

  accepts_nested_attributes_for :recipe_ingredients, :directions
  before_save :erase_empty_directions, :erase_empty_ingredients, :erase_empty_recipe_ingredients
  before_destroy :delete_children

  def erase_empty_recipe_ingredients
    self.recipe_ingredients = self.recipe_ingredients.select {|ri| ri.ingredient.title != ''}
  end

  def erase_empty_ingredients
    self.ingredients = self.ingredients.select {|i| i.title != ''}
  end

  def erase_empty_directions
    self.directions = self.directions.select {|d| d.text != ''}
  end

  def delete_children
    self.directions.each {|d| d.destroy}
    self.recipe_ingredients.each {|ri| ri.destroy}
    self.reports.each {|report| report.destroy}
  end
end
```


#### **`RecipeIngredient`**

In the same way as `Recipe`, this model need to access `Ingredient` because `Ingredient` model will be created at `/recipes/new` through `RecipeIngredient`. In addition, the method `#ingredient_attributes=` should not only "create" new ingredient but "find or create". So I need to define `#ingredient_attributes=` by myself.

```
# app/models/recipe_ingredient.rb

class RecipeIngredient < ApplicationRecord
  ...
	
  accepts_nested_attributes_for :ingredient

  def ingredient_attributes=(attributes)
    self.ingredient = Ingredient.find_or_create_by(title: attributes["title"].downcase)
  end
end
```


#### **`Ingredient`**

`Ingredient` should be uniq so this need `validates`.

```
# app/models/ingredient.rb

class Ingredient < ApplicationRecord
  ...
	
  validates :title, uniqueness: { case_sensitive: false }
end
```


#### **`Report`**

`Report`'s `rate` attribute must be present so this need `validates`.

```
# app/models/report.rb

class Report < ApplicationRecord
  ...
	
  validates :rate, presence: true
end
```


#### **`User`**

`User` must has both `username` and `email` and I'd like the `password` to be 6 to 30 characters long. The `presence` of `password` is already set to be true by `has_secure_password`, but if user is created with OAuth, it need not have `password`. 

```
# app/models/user.rb

class User < ApplicationRecord
  ...

  validates :username, :email, presence: true, uniqueness: true
  validates :password, presence: true, length: {in: 6..30}, unless: Proc.new { |user| user.uid.present? }

  before_destroy :delete_children

  def delete_children
    self.recipes.each {|recipe| recipe.destroy}
    self.reports.each {|report| report.destroy}
  end
end
```


### 8. Writing Views

Before writing views, let me add new gem and new css file because I'm going to use [bootstrap](http://getbootstrap.com/).

```
# Gemfile

...
gem 'bootstrap-sass', '~> 3.3', '>= 3.3.7'
...
```

```
# app/assets/stylesheets/custom.scss

@import "bootstrap-sprockets";
@import "bootstrap";
```

And I need some helpers to use in views.

```
# app/helpers/sessions_helper.rb

module SessionsHelper
  def logged_in?
    !!session[:user_id]
  end

  def current_user
    User.find(session[:user_id])
  end
end
```

Now let me start writing views as the requirements in section 3.

#### **`layouts`**

**application.html.erb**

```
# app/views/layouts/application.html.erb

...
  <body>
    <header class="navbar navbar-fixed-top navbar-inverse">
      <div class="container">
        <%= link_to "Recipe Manager", '/', id: "logo" %>
        <nav>
          <% if logged_in? %>
            <p class="navbar-text">Signed in as <%= current_user.username %></p>
          <% end %>
          <ul class="nav navbar-nav navbar-right">
            <li><%= link_to "Home", '/', id:"home" %></li>
            <% if logged_in? %>
              <li><%= link_to "My Page", user_path(current_user) %></li>
              <li><%= link_to "Sign out", signout_path %></li>
            <% else %>
              <li><%= link_to "Sign up", signup_path %></li>
              <li><%= link_to "Sign in", signin_path %></li>
            <% end %>
          </ul>
        </nav>
      </div>
    </header>
    <div class="container">
      <% flash.each do |message_type, message| %>
        <div class="alert alert-<%= message_type %>"><%= message %></div>
      <% end %>
      <%= yield %>
    </div>
  </body>...
```


#### **`recipes`**


**index.html.erb (`/recipes`)**

```
# app/views/reports/index.html.erb

<h1>
  Recipes
  <div class="recipe-editor">
    <% if logged_in? %>
      <%= link_to "Add new recipe", new_recipe_path, class: "btn btn-sm btn-primary" %>
    <% end %>
  </div>
</h1>

<div class="recipes">
  <ul>
    <%= render partial: "shared/recipes", collection: @recipes, as: :recipe %>
    <%= render partial: "shared/recipes", collection: @no_repo, as: :recipe if @no_repo %>
  </ul>
</div>
```

**show.html.erb (`/recipes/:id`)**

```
# app/views/recipes/show.html.erb

<div class="recipe-title">
  <h1>
    <%= @recipe.title %>
    <div class="recipe-editor">
      <% if logged_in? && @recipe.creator == current_user %>
        <%= link_to "Edit", edit_recipe_path(@recipe), class: "btn btn-sm btn-primary" %>
        <%= link_to "Delete", recipe_path(@recipe), method: :delete, class: "btn btn-sm btn-default" %>
      <% end %>
    </div>
  </h1>
  <h3><%= @recipe.time %>min | <%= render partial:"shared/lg_rate", locals:{rate: @recipe.average_rate} %> | <%= @recipe.reports.count %> reports</h3>
  <p>Recipe by: <%= link_to @recipe.creator.username, user_path(@recipe.creator) %></p>

</div>

<div class="ingredients">
  <h2>Ingredients</h2>
  <ul>
    <% @recipe.recipe_ingredients.each do |ri| %>
      <li><%= ri.ingredient.title %> - <%= ri.quantity %> <%= ri.unit %></li>
    <% end %>
  </ul>
</div>

<div class="directions">
  <h2>Directions</h2>
  <ol>
    <% @recipe.directions.each do |d| %>
      <li><%= d.text %></li>
    <% end %>
  </ol>
</div>

<div class="reports">
  <h2>
    Reports
    <div class="report-editor">
      <%= link_to "Add new report", new_recipe_report_path(@recipe), class: "btn btn-sm btn-primary" %>
    </div>
  </h2>

  <ul>
    <%= render partial: "shared/reports", collection: @recipe.reports, as: :report %>
  </ul>

  <%= link_to "See all reports", recipe_reports_path(@recipe) %>
</div>
```

**new.html.erb (`/recipes/new`) (needs log-in)**

```
# app/views/recipes/new.html.erb

<h1>Add new recipe</h1>

<%= render partial: "form", locals: {recipe: @recipe, submit_btn: "Create new recipe"} %>

```


**edit.html.erb (`/recipes/:id/edit`) (needs to be owner)**

```
# app/views/recipes/edit.html.erb

<h1>Edit your recipe</h1>

<%= render partial: "form", locals: {recipe: @recipe, submit_btn: "Update"} %>
```

**partials**

```
# app/views/recipes/_form.html.erb

<div class="form-field">
  <%= form_for(recipe) do |f| %>
    <%= render 'shared/errors', object: f.object %>
    <div class="form-group">
      <%= f.label :title %>
      <%= f.text_field :title, class: "form-control" %>
    </div>

    <%= f.label "Ingredients" %>
    <ul>
      <%= f.fields_for(:recipe_ingredients) do |rif| %>
        <li>
          <div class="form-inline">
            <%= rif.fields_for(:ingredient) do |ingf| %>
              <div class="form-group">
                <%= ingf.label :title, "Name" %>
                <%= ingf.text_field :title, class: "form-control" %>
              </div>
            <% end %>

            <div class="form-group">
              <%= rif.label :quantity %>
              <%= rif.number_field :quantity, class: "form-control" %>
            </div>

            <div class="form-group">
              <%= rif.label :unit %>
              <%= rif.text_field :unit, class: "form-control" %>
            </div>
          </div>
        </li>
      <% end %>
    </ul>
    <br>

    <%= f.label "Directions" %>
    <ol>
      <%= f.fields_for(:directions) do |df| %>
        <div class="form-group">
          <li><%= df.text_field :text, class: "form-control" %></li>
        </div>
      <% end %>
    </ol>

    <%= f.hidden_field(:user_id, value: current_user.id) %>

    <div class="form-group">
      <%= f.submit submit_btn, class: "btn btn-primary" %>
    </div>
  <% end %>
</div>
```


#### **`reports`**

**index.html.erb (`/recipes/:recipe_id/reports`)**

```
# app/views/reports/index.html.erb

<h1>
  Reports for <%= link_to @recipe.title, recipe_path(@recipe) %>
  <div class="report-editor">
    <%= link_to "Add new report", new_recipe_report_path(@recipe), class: "btn btn-sm btn-primary" %>
  </div>
</h1>

<ul>
  <%= render partial: "shared/reports", collection: @reports, as: :report %>
</ul>
```

**new.html.erb (`/recipes/:recipe_id/reports/new`) (needs log-in)**

```
# app/views/reports/new.html.erb

<h1>Add new report</h1>

<%= render partial: "form", locals: {report: @report, url: recipe_reports_path(@recipe)} %>
```

**edit.html.erb (`/reports/:id/edit`) (needs to be owner)**

```
# app/views/reports/edit.html.erb

<h1>Edit my report</h1>

<%= render partial: "form", locals: {report: @report, url: report_path(@report)} %>
```

**partials**

```
# app/views/reports/_form.html.erb

<p><strong>To recipe:</strong> <%= link_to(report.recipe.title) %></p>

<div class="form-field">
  <%= form_for(report, url: url) do |f|%>
    <%= render 'shared/errors', object: f.object %>
    <div class="form-group">
      <%= f.label(:rate) %>
      <%= f.number_field(:rate, in: 1...6) %>
    </div>

    <div class="form-group">
      <%= f.label(:comment) %>
      <%= f.text_field(:comment) %>
    </div>

    <%= f.hidden_field(:user_id, value: current_user.id) %>

    <div class="form-group">
      <%= f.submit "Add new report", class: "btn btn-primary" %>
    </div>
  <% end %>
</div>
```

#### **`users`**

**show.html.erb (`/users/:id`)**

```
# app/views/users/show.html.erb

<h1>
  <%= @user.username %>
  <div class="user-editor">
    <% if logged_in? && @user == current_user %>
      <%= link_to "Edit", edit_user_path(@user), class: "btn btn-sm btn-primary" %>
      <%= link_to "Delete", user_path(@user), method: :delete, class: "btn btn-sm btn-default" %>
    <% end %>
  </div>
</h1>

<h2>Recipes</h2>

<div class="recipes">
  <ul>
    <%= render partial: "shared/recipes", collection: @recipes, as: :recipe %>
  </ul>
</div>

<h2>Reports</h2>

<div class="reports">
  <ul>
    <%= render partial: "shared/reports", collection: @reports, as: :report %>
  </ul>
</div>
```

**new.html.erb (`/signup`)**

```
# app/views/users/new.html.erb

<h1>Sign up</h1>
<%= render partial: "form", locals: {user: @user, submit_btn: "Sign up"} %>
```

**edit.html.erb (`/users/:id/edit`) (needs to be owner)**

```
# app/views/users/edit.html.erb

<h1>Edit user</h1>
<%= render partial: "form", locals: {user: @user, submit_btn: "Update"} %>
```

**partials**

```
# app/views/users/_form.html.erb

<div class="form-field">
  <%= form_for(user) do |f| %>
    <%= render 'shared/errors', object: f.object %>
    <div class="form-group">
      <%= f.label :username %>
      <%= f.text_field :username, class: "form-control" %>
    </div>

    <div class="form-group">
      <%= f.label :email %>
      <%= f.text_field :email, class: "form-control" %>
    </div>

    <div class="form-group">
      <%= f.label :password %>
      <%= f.text_field :password, class: "form-control" %>
    </div>

    <div class="form-group">
      <%= f.submit submit_btn, class: "btn btn-primary" %>
    </div>
  <% end %>
</div>
```

#### **`sessions`**

**new.html.erb (`/signin`)**

```
# app/views/sessions/new.html.erb

<h1>Sign in</h1>

<div class="form-field">
  <%= form_for(:session, url: signin_path, method: :post) do |f| %>
    <div class="form-group">
      <%= f.label :email %>
      <%= f.text_field :email, class: "form-control", value: @input %>
    </div>

    <div class="form-group">
      <%= f.label :password %>
      <%= f.text_field :password, class: "form-control" %>
    </div>

    <div class="form-group">
      <%= f.submit "Sign in", class: "btn btn-primary" %>
    </div>
  <% end %>
</div>
```

#### **`shared`**
​
**partials**

```
# app/views/shared/_recipes.html.erb

<li>
  <h2>
    <%=  link_to recipe.title, recipe_path(recipe), id:"recipe_title" %>
    <div class="recipe-editor">
      <% if logged_in? && recipe.creator == current_user %>
        <%= link_to "Edit", edit_recipe_path(recipe), class: "btn btn-sm btn-primary" %>
        <%= link_to "Delete", recipe_path(recipe), method: :delete, class: "btn btn-sm btn-default" %>
      <% end %>
    </div>
  </h2>
  <div>
    <%= recipe.time %>min | <%= render partial:"shared/rate", locals:{rate: recipe.average_rate} %> | <%= recipe.reports.count %> reports -- Recipe by: <%= link_to recipe.creator.username, user_path(recipe.creator) %>
  </div>
</li>
```

```
# app/views/shared/_reports.html.erb

<li>
  <%= render partial:"shared/rate", locals:{rate: report.rate} %> by <%= link_to report.user.username, user_path(report.user) %>
  <div class="report-editor">
    <% if logged_in? && report.user == current_user %>
      <%= link_to "Edit", edit_report_path(report), class: "btn btn-xs btn-primary" %>
      <%= link_to "Delete", report_path(report), method: :delete, class: "btn btn-xs btn-default" %>
    <% end %>
  </div>
  <p><%= report.comment %></p>
</li>
```

```
# app/views/shared/_errors.html.erb

<% if object.errors.any? %>
  <div id="form_error">
    <div class="alert alert-danger">
      The form contains <%= pluralize(object.errors.count, "error") %>.
    </div>
    <ul>
    <% object.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
```

```
# app/views/shared/_rate.html.erb

<% if rate >= 4.8 %>
  <div class="sm-star-icon sm-star-5-0"></div>
<% elsif rate >= 4.3 %>
  <div class="sm-star-icon sm-star-4-5"></div>
<% elsif rate >= 3.8 %>
  <div class="sm-star-icon sm-star-4-0"></div>
<% elsif rate >= 3.3 %>
  <div class="sm-star-icon sm-star-3-5"></div>
<% elsif rate >= 2.8 %>
  <div class="sm-star-icon sm-star-3-0"></div>
<% elsif rate >= 2.3 %>
  <div class="sm-star-icon sm-star-2-5"></div>
<% elsif rate >= 1.8 %>
  <div class="sm-star-icon sm-star-2-0"></div>
<% elsif rate >= 1.3 %>
  <div class="sm-star-icon sm-star-1-5"></div>
<% elsif rate >= 0.8 %>
  <div class="sm-star-icon sm-star-1-0"></div>
<% elsif rate >= 0.3 %>
  <div class="sm-star-icon sm-star-0-5"></div>
<% else %>
  <div class="sm-star-icon sm-star-0-0"></div>
<% end %>
```

```
# app/views/shared/_lg_rate.html.erb

<% if rate >= 4.8 %>
  <div class="lg-star-icon lg-star-5-0"></div>
<% elsif rate >= 4.3 %>
  <div class="lg-star-icon lg-star-4-5"></div>
<% elsif rate >= 3.8 %>
  <div class="lg-star-icon lg-star-4-0"></div>
<% elsif rate >= 3.3 %>
  <div class="lg-star-icon lg-star-3-5"></div>
<% elsif rate >= 2.8 %>
  <div class="lg-star-icon lg-star-3-0"></div>
<% elsif rate >= 2.3 %>
  <div class="lg-star-icon lg-star-2-5"></div>
<% elsif rate >= 1.8 %>
  <div class="lg-star-icon lg-star-2-0"></div>
<% elsif rate >= 1.3 %>
  <div class="lg-star-icon lg-star-1-5"></div>
<% elsif rate >= 0.8 %>
  <div class="lg-star-icon lg-star-1-0"></div>
<% elsif rate >= 0.3 %>
  <div class="lg-star-icon lg-star-0-5"></div>
<% else %>
  <div class="lg-star-icon lg-star-0-0"></div>
<% end %>
```

### 9. Writing Controllers

I write controllers as the requirements in section 4.


#### **`recipes_controller`**

```
# app/controllers/recipes_controller.rb

class RecipesController < ApplicationController
  def index
    @recipes = Recipe.all
    @search = Search.new
  end

  def new
    @recipe = Recipe.new
    6.times {
      @recipe.recipe_ingredients.build.build_ingredient
      @recipe.directions.build
    }
  end

  def create
    @recipe = Recipe.create(recipe_params)
    if @recipe.save
      flash[:success] = "Added new recipe."
      redirect_to recipe_path(@recipe)
    else
      render :new
    end
  end

  def show
    @recipe = Recipe.find(params[:id])
  end

  def edit
    @recipe = Recipe.find(params[:id])
    6.times {
      @recipe.recipe_ingredients.build.build_ingredient
      @recipe.directions.build
    }
  end

  def update
    @recipe = Recipe.find(params[:id])
    if @recipe.update(recipe_params)
      flash[:success] = "Updated recipe."
      redirect_to recipe_path(@recipe)
    else
      render :edit
    end
  end

  def destroy
    Recipe.find(params[:id]).destroy
    flash[:success] = "Deleted recipe."
    redirect_to recipes_path
  end

  private

  def recipe_params
    params.require(:recipe).permit(
      :title,
      :user_id,
      recipe_ingredients_attributes: [
        :id,
        :quantity,
        :unit,
        ingredient_attributes: [:id, :title]
      ],
      directions_attributes: [:id, :text]
    )
  end
end
```

#### **`reports_controller`**

```
# app/controllers/reports_controller.rb

class ReportsController < ApplicationController
  def index
    @recipe = Recipe.find(params[:recipe_id])
    @reports = @recipe.reports
  end

  def new
    @recipe = Recipe.find(params[:recipe_id])
    @report = @recipe.reports.build
  end

  def create
    @recipe = Recipe.find(params[:recipe_id])
    @report = @recipe.reports.build(report_params)
    if @report.save
      flash[:success] = "Added new report."
      redirect_to recipe_path(@recipe)
    else
      render :new
    end
  end

  def edit
    @report =  Report.find(params[:id])
  end

  def update
    @report =  Report.find(params[:id])
    if @report.update(report_params)
      flash[:success] = "Updated report."
      redirect_to recipe_path(@report.recipe)
    else
      render :edit
    end
  end

  def destroy
    report =  Report.find(params[:id])
    recipe = report.recipe
    report.destroy
    flash[:success] = "Deleted report."
    redirect_to recipe_path(recipe)
  end

  private

  def report_params
    params.require(:report).permit(:id, :rate, :comment, :user_id)
  end

  def only_owner
    report = Report.find(params[:id])
    unless logged_in? && report.user_id == session[:user_id]
      flash[:danger] = "That page is only for owners."
      redirect_back(fallback_location: recipe_path(report.recipe))
    end
  end
end
```

#### **`users_controller`**

```
# app/controllers/users_controller.rb

class UsersController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @user = User.new(user_params)
    if @user.save
      session[:user_id] = @user.id
      flash[:success] = "Successfully signed up."
      redirect_to user_path(@user)
    else
      render :new
    end
  end

  def show
    @user = User.find(params[:id])
    @recipes = @user.recipes
    @reports = @user.reports
  end

  def edit
    @user = User.find(params[:id])
  end

  def update
    @user = User.find(params[:id])
    if @user.update(user_params)
      flash[:success] = "Successfully updated."
      redirect_to user_path(@user)
    else
      render :edit
    end
  end

  def destroy
    User.find(params[:id]).destroy
    session.delete(:user_id)
    flash[:success] = "Deleted user."
    redirect_to root_url
  end

  private

  def user_params
    params.require(:user).permit(:id, :username,:email,:password)
  end
end
```

#### **`sessions_controller`**

```
# app/controllers/sessions_controller.rb

class SessionsController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @input = session_params[:email]
    user = User.find_by(email: @input)
    if user.try(:authenticate, session_params[:password])
      session[:user_id] = user.id
      flash[:success] = "Successfully signed in."
      redirect_back_or(user_path(user))
    else
      flash.now[:danger] = "Invalid email/password combination."
      render :new
    end
  end

  def destroy
    session.delete(:user_id)
    flash[:success] = "Signed out."
    redirect_to root_url
  end

  private

  def session_params
    params.require(:session)
  end
end
```

### 10. Adding authorization

Next, let me add autorization not to offer user-only method to visitor.

#### **`sessions_helper`**

Because controllers shouldn't be fat, I'd like to define methods in `SessionsHelper`.

```
# app/helpers/sessions_helper.rb

module SessionsHelper
  def logged_in?
    !!session[:user_id]
  end

  def current_user
    User.find(session[:user_id])
  end

  def need_login
    unless logged_in?
      store_path
      flash[:danger] = "Please sign in to access that page."
      redirect_to signin_path
    end
  end

  def store_path
    session[:forwarding_path] = request.fullpath if request.get?
  end

  def redirect_back_or(default)
    redirect_to(session[:forwarding_path] || default)
    session.delete(:forwarding_path)
  end

  def only_owner
    recipe = Recipe.find(params[:id])
    unless logged_in? && recipe.user_id == session[:user_id]
      flash[:danger] = "That page is only for owners."
      redirect_back(fallback_location: root_path)
    end
  end
end
```

Add `SessionsHelper` to `ApplicationController`.

```
# app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  include SessionsHelper
end
```

#### **`recipes_controller`, `reports_controller`**

Then, add `before_action` to controllers.

```
# app/controllers/recipes_controller.rb

class RecipesController < ApplicationController
  before_action :need_login, except: [:index, :show, :search]
  before_action :only_owner, only: [:edit, :update, :destroy]

  ...
end
```

```
# app/controllers/reports_controller.rb

class ReportsController < ApplicationController
  before_action :need_login, except: [:index, :show]
  before_action :only_owner, only: [:edit, :update, :destroy]

  ...
end
```


### 11. Adding search box

#### **`Recipe` model**

First, I'm going to define some methods in `Recipe` model to filter recipes.

To know the average rate of the recipe:

```
  def average_rate
    if reports.size == 0
      return 0
    else
      sum = 0
      reports.each do |report|
        sum += report.rate
      end
      sum / reports.size
    end
  end
```

To sort recipes:

```
  def self.order_by_rate
    self.joins(:reports)
		.select(
		  "'recipes'.'id', 
		  	'recipes'.'user_id', 
		  	'recipes'.'title', 
		  	'recipes'.'time', 
		  	'recipes'.'link',
		  	'reports'.'recipe_id', 
		  	AVG('reports'.'rate')"
	  )
		.group("'reports'.'recipe_id'")
		.order("AVG('reports'.'rate') DESC")
  end

  def self.order_by_time
    self.order(:time)
  end
```

To get recipes with report:

```
  def self.has_report
    self.where(id: Report.pluck(:recipe_id).uniq)
  end
```

To get recipes without report:

```
  def self.has_no_report
    self.where.not(id: Report.pluck(:recipe_id).uniq)
  end
```

To get recipes by rate:

```
  def self.rate_over(int)
    self.joins(:reports)
		.select(
		  "'recipes'.'id', 
   			'recipes'.'user_id', 
	  		'recipes'.'title', 
				'recipes'.'time', 
				'recipes'.'link',
				'reports'.'recipe_id', 
				AVG('reports'.'rate')"
		)
		.group("'reports'.'recipe_id'")
		.having("AVG('reports'.'rate') >= ?", int)
  end
```

To get recipes by time:

```
  def self.time_over(int)
    self.where("time >= ?", int)
  end

  def self.time_under(int)
    self.where("time <= ?", int)
  end
```

To get recipes by the creator:

```
  def self.by_creator(user_id)
    self.where(user_id: user_id)
  end
```

To get recipes need the ingredient:

```
  def self.by_ingredient(ingredient_id)
    self.where(
  		id: 
			RecipeIngredient.where(
			  ingredient_id: ingredient_id
			).pluck(:recipe_id).uniq
		)
  end
```

To get recipes have a report by the user:

```
  def self.has_report_by(user_id)
    self.joins(:reports).where("'reports'.'user_id' = ?", user_id)
  end
```


#### **`Search` model**

I'd like to make `Search` model to simplify search form.

```
# app/models/search.rb

class Search
  include ActiveModel::Model

  ATTRIBUTES = %i(
    order_by
    has_report
    rate_over
    rate_under
    time_over
    time_under
    by_creator
    by_ingredient
    has_report_by
  )
  attr_accessor(*ATTRIBUTES)

  def filter
    result = Recipe.all
    result = result.time_over(time_over.to_i) if time_over.present?
    result = result.time_under(time_under.to_i) if time_under.present?
    result = result.by_creator(by_creator.to_i) if by_creator.present?
    result = result.by_ingredient(by_ingredient.to_i) if by_ingredient.present?
    result = result.has_report_by(has_report_by.to_i) if has_report_by.present?
    if (order_by == "rate" && has_report == "0" && !rate_over.present?)
      $no_repo = result.has_no_report
    end
    result = result.order_by_rate if order_by == "rate"
    result = result.order_by_time if order_by == "time"
    result = result.has_report if has_report == "1"
    result = result.rate_over(rate_over.to_i) if rate_over.present?
    result
  end
end
```

#### **`recipes/index.html.erb`**

Add search box to recipes/index view.

```
# app/views/recipes/index.html.erb

<h1>...</h1>

<div class="search-area panel panel-default">
  <div class="panel-heading">Search recipes</div>
  <div class="panel-body">
    <%= form_for(@search, url: search_path, method: "GET") do |f| %>
      <div class="form-group form-inline">
        <div class="form-group">
          <%= f.label(:order_by, "Sort by") %>
          <%= f.select(:order_by, [["Rate", "rate"], ["Time", "time"]], {include_blank: 'Select'}) %>
        </div>
        <div class="checkbox" id="only">
          <%= f.check_box(:has_report) %>
          <%= f.label(:has_report, "Only recipes with report") %>
        </div>
      </div>
      <div class="form-group">
        <%= f.label(:rate_over, "Rate: over") %>
        <%= f.number_field(:rate_over, in: 1...6) %>
      </div>
      <div class="form-group form-inline">
        <%= f.label(:time_under, "Time: from") %>
        <%= f.number_field(:time_under) %>
        <%= f.label(:time_over, "to") %>
        <%= f.number_field(:time_over) %>
      </div>
      <div class="form-group">
        <%= f.label(:by_ingredient, "Ingredient:") %>
        <%= f.select(:by_ingredient, Ingredient.has_recipe.collect {|i| [ i.title, i.id ] }, {include_blank: 'Select Ingredient'}) %>
      </div>
      <div class="form-group form-inline">
        <%= f.label(:by_creator, "Creator:") %>
        <%= f.select(:by_creator, User.only_creator.collect {|u| [ u.username, u.id ] }, {include_blank: 'Select Creator'}) %>
      </div>
      <div class="form-group">
        <%= f.label(:has_report_by, "has report by") %>
        <%= f.select(:has_report_by, User.has_report.collect {|u| [ u.username, u.id ] }, {include_blank: 'Select User'}) %>
      </div>
      <%= f.submit("Search", class: "btn btn-primary") %>
      <%= link_to "Clear", root_path, class: "btn btn-sm btn-default" %>
    <% end %>
  </div>
</div>

<div class="recipes">...</div>
```

#### **`route.rb`**

Add `/search` route.

```
# config/routes.rb

Rails.application.routes.draw do
  ...
  get 'search', to: 'recipes#search'
  ...
end
```


#### **`recipes_controller`**

Add search method to recipes_controller.

```
# app/controllers/recipes_controller.rb

class RecipesController < ApplicationController
  ...

  def index
    @recipes = Recipe.all
    @search = Search.new
  end

  def search
    @search = Search.new(search_params)
    @recipes = @search.filter
    @no_repo = $no_repo
    $no_repo = nil
    render :index
  end

  ...
end
```

### 12. Adding OAuth by `omniauth`

I'll offer google oauth and facebook oauth to log-in by using gem `omniauth`.

Add omniauth to `Gemfile`.

```
# Gemfile

source 'https://rubygems.org'
...

gem "omniauth-google-oauth2"
gem 'omniauth-facebook'

...
```

Create `omniauth.rb` file.

```
# config/initializers/omniauth.rb

Rails.application.config.middleware.use OmniAuth::Builder do
  provider :google_oauth2, ENV["GOOGLE_CLIENT_ID"], ENV["GOOGLE_CLIENT_SECRET"], {
    :scope => 'email'
  }
  provider :facebook, ENV['FACEBOOK_KEY'], ENV['FACEBOOK_SECRET']
end
```

Get ID and secret from google and facebook.

> Google: [Creating a Google API Console project and client ID](https://developers.google.com/identity/sign-in/web/devconsole-project)
> Facebook: [Register and Configure an App](https://developers.facebook.com/docs/apps/register)

Hand these keys to ENV in Terminal.

```
export GOOGLE_CLIENT_ID=[your id]
export GOOGLE_CLIENT_SECRET=[yout secret]
export FACEBOOK_KEY=[your key]
export FACEBOOK_SECRET=[your secret]
```


Add link to `/signin` and `/signup`.

```
# app/views/users/new.html.erb

<h1>Sign up</h1>
<%= render partial: "form", locals: {user: @user, submit_btn: "Sign up"} %>

<%= link_to("Sign up with google", "/auth/google_oauth2", class: "btn btn-default") %>
<%= link_to("Sign up with facebook", "/auth/facebook", class: "btn btn-default") %>
```

```
# app/views/sessions/new.html.erb

<h1>Sign in</h1>

<div class="form-field">...</div>

<%= link_to("Sign in with google", "/auth/google_oauth2", class: "btn btn-default") %>
<%= link_to("Sign up with facebook", "/auth/facebook", class: "btn btn-default") %>
```
 

Add route.

```
# config/routes.rb

Rails.application.routes.draw do
  ...
	
  get '/auth/google_oauth2/callback', to: 'sessions#oauth'
  get '/auth/facebook/callback', to: 'sessions#oauth'
end
```

Add `auth` to SessionsController.

```
# app/controllers/sessions_controller.rb

class SessionsController < ApplicationController
  ...

  def oauth
    auth = request.env["omniauth.auth"]
    @user = User.find_or_create_by(uid:auth[:uid]) do |u|
      u.username = auth[:info][:name]
      u.email = auth[:info][:email]
    end
    if @user.save
      session[:user_id] = @user.id
      flash[:success] = "Successfully signed up."
      redirect_back_or(user_path(@user))
    else
      flash.now[:danger] = @user.errors.full_messages_for(:email).join(" / ")
      render :new
    end
  end

  ...
end
```

### 13. Adjusting css

```
# app/assets/stylesheets/custom.scss

@import "bootstrap-sprockets";
@import "bootstrap";

/* body */
body {
  margin-bottom: 50px
}


/* header */

#logo {
  float: left;
  margin-right: 10px;
  font-size: 1.7em;
  color: #fff;
  text-transform: uppercase;
  letter-spacing: -1px;
  padding-top: 9px;
  font-weight: bold;
}

#logo:hover {
  color: #fff;
  text-decoration: none;
}

nav .navbar-text {
  font-style: italic;
}

@media (max-width: 767px) {
  nav {
    clear: both;
    position: relative;
    .navbar-text {
      position: absolute;
      bottom: 0px;
      right: 0px;
      margin-bottom: 7.5px;
      font-size: 12px;
    }
  }

  li a#home {
    display: none;
  }
}

/* container next to header */

@media (max-width: 767px) {
  header + div.container {
    margin-top: 130.5px;
  }
}

@media (min-width: 768px) {
  header + div.container {
    margin-top: 51px;
  }
}

/* searchbox */

.checkbox#only {
  margin-left: 20px;

  input[type="checkbox"] {
    margin-left: 0px;
  }

  label {
    margin-bottom: 5px;
    font-weight: bold;
  }
}

/* recipe */

h1 div.recipe-editor {
  display: inline-block;
  margin-left: 10px;
}

h2 div.recipe-editor {
  display: inline-block;
  margin-left: 10px;
}

h2 div.report-editor {
  display: inline-block;
  margin-left: 5px;
}

/* report */

h1 div.report-editor {
  display: inline-block;
  margin-left: 10px;
}

li div.report-editor {
  display: inline-block;
  margin-left: 5px;
}

/* user */

h1 div.user-editor {
  display: inline-block;
  margin-left: 10px;
}


/* stars */

div.sm-star-icon {
  width: 80px;
  height: 18px;
  background-image: image-url("small_stars.png");
  -webkit-background-size: 339px 20px;
  background-size: 339px 20px;
  background-repeat: no-repeat;
  display: inline-block;
  vertical-align: middle;;
}

div.sm-star-5-0 {
  background-position: -4px;
}

div.sm-star-4-5 {
  background-position: -174px;
}

div.sm-star-4-0 {
  background-position: -20px;
}

div.sm-star-3-5 {
  background-position: -190px;
}

div.sm-star-3-0 {
  background-position: -36px;
}

div.sm-star-2-5 {
  background-position: -206px;
}

div.sm-star-2-0 {
  background-position: -52px;
}

div.sm-star-1-5 {
  background-position: -222px;
}

div.sm-star-1-0 {
  background-position: -68px;
}

div.sm-star-0-5 {
  background-position: -238px;
}

div.sm-star-0-0 {
  background-position: -84px;
}

div.lg-star-icon {
  width: 95px;
  height: 20px;
  background-image: image-url("large_stars.png");
  -webkit-background-size: 339px 20px;
  background-size: 400px 28px;
  background-repeat: no-repeat;
  display: inline-block;
  vertical-align: middle;;
}

div.lg-star-5-0 {
  background-position: -5px;
}

div.lg-star-4-5 {
  background-position: -205px;
}

div.lg-star-4-0 {
  background-position: -24px;
}

div.lg-star-3-5 {
  background-position: -224px;
}

div.lg-star-3-0 {
  background-position: -43px;
}

div.lg-star-2-5 {
  background-position: -243px;
}

div.lg-star-2-0 {
  background-position: -62px;
}

div.lg-star-1-5 {
  background-position: -262px;
}

div.lg-star-1-0 {
  background-position: -81px;
}

div.lg-star-0-5 {
  background-position: -281px;
}

div.lg-star-0-0 {
  background-position: -100px;
}

/* form */

li .form-control {
  display: inline-block;
  margin-bottom: 10px;
}

li .form-inline .form-control {
  margin-right: 10px;
}

div#form_error ul {
  color: #a94442
}

div.field_with_errors input.form-control {
  border-color: #c75351
}

/* alert */

.alert {
  margin-top: 20px;
  margin-bottom: 10px;
}
```

### 14. Completed making RecipeManager

All done!
[RecipeManager(GitHub](https://github.com/ayafushimi/recipe_manager)

