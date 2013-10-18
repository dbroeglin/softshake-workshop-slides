!SLIDE subsection
.notes first slide

# Notre première application Ruby on Rails 

Let's build a TODO app.

!SLIDE small 
# Générons notre première application

    @@@ sh
    $ rails new todo
    $ cd todo
    $ rails server

- ouvrir nouveau terminal.
- ouvrir http://localhost:3000 


!SLIDE bullets
# abracadabra

    @@@ sh
    rails generate scaffold task \
        title:string completed:boolean

![scaffold](scaffold.png)

rafraîchir le browser, : (

!SLIDE bullets small
# l'erreur nous indique la solution 

    @@@ Ruby
    $ rake db:migrate RAILS_ENV=development 

    # db/migrate/20131012094430_create_tasks.rb
    class CreateTasks < ActiveRecord::Migration
      def change
        create_table :tasks do |t|
          t.string :title
          t.boolean :completed

          t.timestamps
        end
      end
    end

- rafraîchir le browser, localhost:3000/tasks

!SLIDE bullets small
# comment ça marche (CRUD "restful" resource)

    @@@ ruby
    # config/routes.rb
    resources :tasks

!SLIDE bullets small
# $ rake routes

    @@@ sh
    
          Prefix Verb   URI Pattern               Controller#Action
        tasks GET    /tasks(.:format)          tasks#index
              POST   /tasks(.:format)          tasks#create
     new_task GET    /tasks/new(.:format)      tasks#new
    edit_task GET    /tasks/:id/edit(.:format) tasks#edit
         task GET    /tasks/:id(.:format)      tasks#show
              PATCH  /tasks/:id(.:format)      tasks#update
              PUT    /tasks/:id(.:format)      tasks#update
              DELETE /tasks/:id(.:format)      tasks#destroy


!SLIDE bullets small
# quel est le chemin d'une nouvelle tâche
  
    @@@ sh
    # app/controller/tasks_controller.rb
    tasks#new -> voir la vue

    # app/views/tasks/new.html.erb


!SLIDE bullets small
# on aimerait la home sur tasks#index

changer config/routes.rb et rajouter

    root "tasks#index"

!SLIDE bullets small
# mieux comprendre les interactions entre les vues et les controlleur

debug params

    @@@ html
    # app/views/layout/application.html.erb
    <body>
      <%= yield %>
      <%= debug(params) if Rails.env.development? %>
    </body>


!SLIDE bullets
# déployons dans les nuages avec Heroku

https://devcenter.heroku.com/articles/quickstart

https://devcenter.heroku.com/articles/getting-started-with-rails4

!SLIDE bullets small
#modifier la gemfile (./gemfile)

rajouter

    @@@ Ruby 
    ruby "1.9.3"

    group :production do
        gem 'pg', '0.15.1'
        gem 'rails_12factor', '0.0.2'
    end

!SLIDE bullets small

remplacer

    @@@ Ruby
    gem 'sqlite3'

    group :development, :test do
        gem 'sqlite3'
    end


!SLIDE bullets small
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

!SLIDE bullets small
# installer les dépendances

    @@@ sh
    $ bundle install


!SLIDE bullets small
# initialiser un repo git

    @@@ sh 
    $ git init
    $ git add .
    $ git commit -m "init"


!SLIDE bullets small
# créer l'app sur heroku
    
    @@@ sh 
    $ heroku create

et déploiement

    @@@ sh
    $ git push heroku master
    $ heroku run rake db:migrate
    $ heroku open

enjoy

!SLIDE bullets small
# make it nice (bootstrap)

rajout au gemfile

    @@@ Ruby
    !SLIDE bullets
    gem 'bootstrap-sass', '2.3.2.0'

installer (restart server après)
    
    bundle install

!SLIDE bullets small
configurer l'asset pipeline

    @@@ Ruby
    # config/application.rb
    module Todo
      class Application < Rails::Application
        .
        .
        .
        config.assets.precompile += %w(*.png *.jpg *.jpeg *.gif)
      end
    end

!SLIDE bullets small
créer un custom css

    app/assets/stylesheets/custom.css.scss

rajouter

    @import "bootstrap";

    body {
        margin: 20pt;
    }

!SLIDE bullets small

enveloper le <%= yield %> dans un div avec class="container" (layout)

!SLIDE bullets small
## partials (créer un header)

créer layouts/_header.html.erb
    
    @@@ html
    <header class="navbar navbar-fixed-top navbar-inverse">
      <div class="navbar-inner">
        <div class="container">
          <%= link_to "JustDidIt", root_path, id: "logo" %>
        </div>
      </div>
    </header>

!SLIDE bullets small
rajouter  <%= render 'layouts/header' %> dans layout

rafraîchir le browser, peut mieux faire...

!SLIDE bullets small
changer custom.css.scss
    
    @@@ css
    #logo {
      float: left;
      margin-right: 10px;
      font-size: 1.7em;
      color: #fff;
      text-transform: uppercase;
      letter-spacing: -1px;
      padding-top: 9px;
      font-weight: bold;
      line-height: 1;
    }

    #logo:hover {
      color: #fff;
      text-decoration: none;
    }

!SLIDE bullets small
et encore

    @@@ css
    body {
      margin: 20pt;
      padding-top: 30px;
    }

!SLIDE bullets small
et encore

rajouter la classe table à la table dans app/views/tasks/index.html.erb



!SLIDE bullets small
# exercises

- rajouter un footer
- styler les link en bouttons

!SLIDE bullets small
# m dans mvc
    @@@ sh
    rails generate model project title:string \
    completed:boolean due_date:date

!SLIDE bullets small
# activerecord
rake db:migrate

ouvrir app/model/project.rb

!SLIDE bullets small

    @@@ Ruby
    #app/models/task.rb
    class Task < ActiveRecord::Base
      belongs_to :project
    end


!SLIDE bullets small
# rails console

rails console 
rails c --sandbox (rollback all db changes on exit)

créer et sauver un projet  
créer et sauver une tache  
Task.create vs Task.create! (how do you handle validation failure)
tenter d'ajouter un projet à une tache et oups  
rails g migration add_project_id_to_task project_id:integer  
rake db:migrate  


!SLIDE bullets small
# test de la vue (intégration de la sélection du projet)

rajouter au gemfile

    gem 'capybara', '2.1.0'

bundle install

!SLIDE bullets small

    @@@ Ruby
    # test/test_helper.rb
    class ActionDispatch::IntegrationTest
      # Make the Capybara DSL available in all integration tests
      include Capybara::DSL
    end

!SLIDE bullets small

    $ rails generate integration_test task_flows

!SLIDE bullets small
édition du test:

    @@@ Ruby
    class TaskFlowsTest < ActionDispatch::IntegrationTest
      test "create new task" do
        project = Project.create(title: 'Test project')

        visit(new_task_path())
        assert page.has_content?('New task')
        within("select[name='task[project_id]']") do
            option = find("option[value='#{project.id}']")
            assert_not option.nil?
            assert_equal project.title, option.text
        end
      end
    end


!SLIDE bullets small
# limite des générateurs.

la création d'un nouvelle tâche permet de setter sa complétion ?
faisons que Task.new.completed == false (valeur par défaut)

!SLIDE bullets small

    @@@ Ruby
    # test/models/task_test.rb
    test "a new task should not be completed" do
      t = Task.new
      assert t.completed == false
    end

!SLIDE bullet small
# une migration
rails g migration add_default_value_to_completed_attribute

!SLIDE bullet small
# le contenu de la migration

    @@@ db/migrate/timestamp_add_default_value_to_completed_attribute
    class AddDefaultValueToCompletedAttribute < ActiveRecord::Migration
      def up
          change_column :tasks, :completed, :boolean, :default => false
      end

      def down
          change_column :tasks, :completed, :boolean, :default => nil
      end
    end

!SLIDE bullet small
# rake test -> green

!SLIDE bullet small
# cleanup de la vue

enlever

    @@@ html
    <div class="field">
      <%= f.label :completed %><br>
      <%= f.check_box :completed %>
    </div>

!SLIDE bullets small
# Exercises

- rajout validation to Task.title (with tests)
- changer rediriger new task sucess sur la home


!SLIDE bullets small
dans Task#index, remplacer show et par "it's done" button

!SLIDE bullets small
    le test

    @@@ ruby
    # test/integration/task_flows_test.rb
    test "task index should allow user to mark task as done" do
      visit(tasks_path)
      assert page.has_link?('I did it') # will fail if no tasks in collection
    end

!SLIDE bullets small
    la route

    @@@ ruby
    # routes.rb
    patch 'tasks/mark_completed/:id' => "tasks#mark_completed", as: :mark_task_completed 

!SLIDE bullets small
    la vue

    @@@ ruby
    # app/views/tasks/index.html.erb
    <td><%= link_to 'I did it', mark_task_completed_path(task), method: :patch %></td>
    <td><%= link_to 'Edit', edit_task_path(task) %></td>
    <td><%= link_to 'Destroy', task, method: :delete, data: { confirm: 'Are you sure?' } %></td>

!SLIDE bullets small
le test

    @@@ ruby
    test "task index should allow user to mark task as done" do
      task_title = "test completion"
      t = Task.create(title: task_title)
      visit(tasks_path)
      assert page.has_link?('I did it') # will fail if no tasks in collection
      dom_element = all(:css, 'tr').last
      within(dom_element) do
          click_on('I did it')
      end
      updated_dom_element = all(:css, 'tr').last
      assert updated_dom_element.has_content?("true")
    end

!SLIDE bullets small
le controller

    @@@ ruby
    def mark_completed
      set_task
      respond_to do |format|
        if @task.mark_as_completed
          puts "yeah"
          format.html { redirect_to tasks_path, notice: 'Task was successfully updated.' }
          format.json { head :no_content }
        else
          format.html { redirect_to tasks_path, notice: 'the task has not been marked complete' }
          format.json { render json: @task.errors, status: :unprocessable_entity }
        end
      end
    end

!SLIDE bullets small
le modèle

    @@@ ruby
    class Task < ActiveRecord::Base
    belongs_to :project
      def mark_as_completed
        update(completed: true)
      end
    end


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


