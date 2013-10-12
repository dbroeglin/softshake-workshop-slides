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


