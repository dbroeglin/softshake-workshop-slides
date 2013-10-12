!SLIDE subsection
.notes first slide

# Notre première application Ruby on Rails 

Let's build a TODO app.

!SLIDE bullets
# Générons notre première application

    @@@ sh
    rails new todo
    cd todo
    rails server
    # ouvrir nouveau terminal.

ouvrir http://localhost:3000 


!SLIDE bullets
# abracadabra
    @@@ sh
    rails generate scaffold task \
        title:string completed:boolean

![scaffold](scaffold.png)

rafraîchir le browser, : (

!SLIDE bullets
# Obey 
    @@@ sh
    rake db:migrate RAILS_ENV=development 

regarder le dossier db/migrate

    db/migrate/20131012094430_create_tasks.rb

    @@@ ruby
    class CreateTasks < ActiveRecord::Migration
      def change
        create_table :tasks do |t|
          t.string :title
          t.boolean :completed

          t.timestamps
        end
      end
    end

rafraîchir le browser, localhost:3000/tasks

!SLIDE bullets
# pourquoi ça marche

regarder config/routes.rb

    @@@ ruby
    resources :tasks

    @@@ sh
    rake routes

          Prefix Verb   URI Pattern               Controller#Action
        tasks GET    /tasks(.:format)          tasks#index
              POST   /tasks(.:format)          tasks#create
     new_task GET    /tasks/new(.:format)      tasks#new
    edit_task GET    /tasks/:id/edit(.:format) tasks#edit
         task GET    /tasks/:id(.:format)      tasks#show
              PATCH  /tasks/:id(.:format)      tasks#update
              PUT    /tasks/:id(.:format)      tasks#update
              DELETE /tasks/:id(.:format)      tasks#destroy

!SLIDE bullets 
# REST

!SLIDE bullets

montrer le chemin de la construction d'une nouvelle task
tasks#new -> voir la vue


!SLIDE bullets
# on aimerait la home sur tasks#index

changer config/routes.rb et rajouter

    root "tasks#index"


!SLIDE bullets
# déployons dans les nuages avec Heroku

https://devcenter.heroku.com/articles/quickstart

https://devcenter.heroku.com/articles/getting-started-with-rails4

!SLIDE bullets
#modifier la gemfile (./gemfile)

rajouter

    @@@ ruby 
    ruby "1.9.3"

    group :production do
        gem 'pg', '0.15.1'
        gem 'rails_12factor', '0.0.2'
    end

remplacer

    @@@ ruby
    gem 'sqlite3'

    group :development, :test do
        gem 'sqlite3'
    end


!SLIDE bullets
# modifier la config db

modifier config/database.yml

    @@@ ruby 
    production:
      adapter: sqlite3
      database: db/production.sqlite3
      pool: 5
      timeout: 5000

    # devient:
    production:
      adapter: postgresql
      encoding: unicode
      database: myapp_production
      pool: 5
      username: myapp
      password:

!SLIDE bullets
# installer les dépendances

    @@@ sh
    $ bundle install


!SLIDE bullets
# initialiser un repo git

    @@@ sh 
    $ git init
    $ git add .
    $ git commit -m "init"


!SLIDE bullets
# créer l'app sur heroku
    
    @@@ sh 
    $ heroku create

et déploiement

    @@@ sh
    $ git push heroku master
    $ heroku run rake db:migrate
    $ heroku open

enjoy

!SLIDE bullets
# make it nice (bootstrap)

rajoute au gemfile

    @@@ ruby
    !SLIDE bullets
    gem 'bootstrap-sass', '2.3.2.0'

installer (restart server après)
    
    bundle install

configurer l'asset pipeline

    @@@ ruby
    module SampleApp
      class Application < Rails::Application
        .
        .
        .
        config.assets.precompile += %w(*.png *.jpg *.jpeg *.gif)
      end
    end

créer un custom css

    app/assets/stylesheets/custom.css.scss

rajouter

    @import "bootstrap";

    body {
        margin: 20pt;
    }






!SLIDE bullets
    @@@ sh
    rails generate model project \
        title:string completed:boolean


!SLIDE bullets
Edit tests/integartion...
    @@@ sh
    rake test 
        title:string completed:boolean

!SLIDE bullets
    @@@ sh
    rails generate model project \
        title:string completed:boolean

!SLIDE bullets
Exercise: add validation to Task.title (with tests)

!SLIDE bullets
Exercise: Add task priorities

!SLIDE subsection
.notes ouvrir un compte pour les tests
# Tweet the task


!SLIDE subsection
.notes vérifier les temps/difficultés de setup
# Deploy on Heroku
 - creer un compte

!SLIDE subsection
# Push to github ?

!SLIDE subsection
.notes live coding
# Java: Deploy in torquebox
 - presentation JRuby
 - utilisation d'une librairie Java
 - service planifié
 - ...


