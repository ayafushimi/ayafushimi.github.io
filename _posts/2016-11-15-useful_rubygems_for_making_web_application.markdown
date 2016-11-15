---
layout: post
title:  "Useful RubyGems for making web application"
date:   2016-11-15 15:25:36 -0500
---

## In this article:
1. Bundler
2. sqlite3
3. ActiveRecord
4. Rake
5. rack
6. shotgun
7. Sinatra


## 1. Bundler
[http://bundler.io/](http://bundler.io/)

### what can do

installs many ruby gems with one command

### how to use

#### 1. install

in Terminal: ```gem install bundler```

#### 2. create Gemfile

in Terminal: ```bundle init```

#### 3. add gems you need to Gemfile

in ```Gemfile```, for example:

```
# frozen_string_literal: true
source "https://rubygems.org"

gem 'nokogiri'
gem 'rails', '3.0.0.beta3'
gem 'rack',  '>=1.0'
gem 'thin',  '~>1.1'
gem 'rspec', :require => 'spec'
gem 'sqlite3'

```

#### 4.run  ```bundle install```

-

## 2. sqlite3
[https://rubygems.org/gems/sqlite3/versions/1.3.11](https://rubygems.org/gems/sqlite3/versions/1.3.11)

### what can do

lets us use SQLite3 via ruby

### how to use

#### 1. install

in Terminal: ```gem install sqlite3```

or add ```gem 'sqlite3’``` to Gemfile

#### 2. open a database

```
require 'sqlite3'

db = SQLite3::Database.new "test.db”
```

#### 3. use SQL statements with ```#execute``` method

```
require 'sqlite3'

sql = <<-SQL
  CREATE TABLE members (
    name TEXT,
    age INTEGER
  );
SQL

db.execute(sql)
```


> ### SQL statements
> [http://www.w3schools.com/sql/sql_syntax.asp\](http://www.w3schools.com/sql/sql_syntax.asp\)
> 
> * SELECT - extracts data from a database
> * UPDATE - updates data in a database
> * DELETE - deletes data from a database
> * INSERT INTO - inserts new data into a database
> * CREATE TABLE - creates a new table
> * ALTER TABLE - modifies a table
> * DROP TABLE - deletes a table

-

## 3. ActiveRecord
[http://guides.rubyonrails.org/active_record_basics.html](http://guides.rubyonrails.org/active_record_basics.html)

### what can do

makes dealing with database easier

### how to use

#### 1. install

in Terminal: ```gem install activerecord ```

or add ```gem ‘activerecord’``` to Gemfile

#### 2. connect to SQLite database

```
require 'active_record'

ActiveRecord::Base.establish_connection(
	:adapter => "sqlite3",
	:database => "../db/dogs"
)
```

more details:
[http://apidock.com/rails/ActiveRecord/Base/establish_connection/class](http://apidock.com/rails/ActiveRecord/Base/establish_connection/class)
[http://apidock.com/rails/ActiveRecord/Base/connection/class](http://apidock.com/rails/ActiveRecord/Base/connection/class)


> #### You can get database object with ```#connection```
> for example:
>
> ```
db = ActiveRecord::Base.connection
db.execute(sql)
```
> 

#### 3. create a Class inherits from ```ActiveRecord::Base```

```
class Member < ActiveRecord::Base
end
```


#### 4. use method defined in ```ActiveRecord::Base```


for example:

```
.create(name: "David", occupation: "Code Artist")
.all
.find_by
.first
#save
#destroy
```


more details:
[http://api.rubyonrails.org/classes/ActiveRecord/Base.html](http://api.rubyonrails.org/classes/ActiveRecord/Base.html)

-

## 4. Rake (Ruby Make)
[http://rake.rubyforge.org/](http://rake.rubyforge.org/)

### what can do

automates programing jobs such as migration

### how to use

#### 1. install

in Terminal: ```gem install rake```

or add ```gem ‘rake’``` to Gemfile

#### 2. make ```Rakefile```

create ```Rakefile``` and write code for tasks which you want to automate in it

for example:

```
task :default => [:test]

task :test do
  ruby "test/unittest.rb"
end
```

#### 3. run ```rake``` or ’taskname’

for example,

to run ‘default’ task: ```rake```

to run ‘test’ task: ```test```


#### + Rake with ActiveRecord
ActiveRecord has defined some rake tasks such as:
```
rake db:create              # Creates the database from DATABASE_URL or con...
rake db:create_migration    # Create a migration (parameters: NAME, VERSION)
rake db:drop                # Drops the database from DATABASE_URL or confi...
rake db:migrate             # Migrate the database (options: VERSION=x, VER...
rake db:migrate:status      # Display status of migrations
rake db:rollback            # Rolls the schema back to the previous version...
rake db:seed                # Loads the seed data from db/seeds.rb
rake db:version             # Retrieves the current schema version number
```

##### how to use

We can use these command by wrinting below codes.

in Gemfile:
```
source "https://rubygems.org"

gem 'rake'
gem 'activerecord', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
```

in Rakefile:
```
require 'active_record'
require 'sinatra/activerecord/rake'
```

-

## 5. rack
[https://rack.github.io/](https://rack.github.io/)

### what can do

provides webserver interface that can read Ruby

### how to use

#### 1. install

in Terminal: ```gem install rack```

or add ```gem ‘rack’``` to Gemfile

#### 2. make ```config.ru```

#### 3. create new class that responds to ```#call```method

for example:

in ```config.ru```

```
require 'rack'

class HelloWorld
  def call(env)
    [200, {"Content-Type" => "text/plain"}, ["Hello world!"]]
  end
end

run HelloWorld.new
```

#### 4. run ```rackup config.ru```

#### 5. open the URL displayed in prompt in a browser

#### 6.ctrl-C to finish rack

-

## 6. shotgun
[https://rubygems.org/gems/shotgun/versions/0.9.2](https://rubygems.org/gems/shotgun/versions/0.9.2)

### what can do

improving rack, reloads updated file without restarting command

### how to use

#### 1. install
in Terminal: ```gem install shotgun```

or add ```gem ‘shotgun’``` to Gemfile

#### 2. make ```config.ru``` file like ```rack```

#### 3. create new class that responds to ```#call```method  like ```rack```

for example:

in ```config.ru```

```
require 'shotgun'

class HelloWorld
  def call(env)
    [200, {"Content-Type" => "text/plain"}, ["Hello world!"]]
  end
end

run HelloWorld.new
```

#### 4. run ```shotgun config.ru```

#### 5. open the URL displayed in prompt in a browser

#### 6.ctrl-C to finish shotgun

-

## 7. Sinatra
[http://www.sinatrarb.com/intro.html](http://www.sinatrarb.com/intro.html)

### what can do

makes creating web apps quicker

### how to use

#### 1. install
in Terminal: ```gem install sinatra```

or add ```gem ‘sinatra’``` to Gemfile

#### 2. create app file

for example:

File: app.rb

```
require ‘sinatra’

get '/' do
  "Hello, World!"
end
```

#### 3. run ```ruby app.rb```

#### 4. Ctrl-C to stop sinatra

#### + Sinatra with rack/shotgun
To see the app in the browser, usually run sinatra with rack or shotgun.

##### how to use

in app.rb file:

```
require ‘sinatra’

class App < Sinatra::Base
  get '/' do
    "Hello, World!"
  end
end
```

in config.ru  file:

```
require ‘shotgun’

run App
```

