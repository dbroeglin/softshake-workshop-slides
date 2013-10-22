!SLIDE subsection
.notes first slide

# Notre première application Ruby on Rails 
«Let's build a TODO app!»


!SLIDE bullets small 
# Génération de l'application

- Ouvrir un nouveau terminal (sous windows ouvrir "Command prompt with 
ruby on rails") et taper :

        @@@ sh
        rails new todo
        cd todo
        rails server

- Ouvrir [http://localhost:3000](http://localhost:3000)


!SLIDE small
.notes TODO: translate 
## Structure d'un projet rails

Source: [guide rails officiel](http://guides.rubyonrails.org/getting_started.html#creating-the-blog-application)

<table class="files">
  <thead><tr>
    <th>File/Folder</th>
    <th>Purpose</th>
  </tr></thead>
  <tbody>
    <tr>
      <td>app/</td>
      <td>
      Contains the controllers, models, views, helpers, mailers and
      assets for your application. You'll focus on this folder for the
      remainder of this guide.</td>
    </tr>
    <tr>
      <td>bin/</td>
      <td>Contains the rails script that starts your app and can contain other scripts you use to deploy or run your application.</td>
    </tr>
    <tr>
      <td>config/</td>
      <td>Configure your application's runtime rules, routes, database, and more.  This is covered in more detail in <a href="configuring.html">Configuring Rails Applications</a>
      </td>
    </tr>
    <tr>
      <td>config.ru</td>
      <td>Rack configuration for Rack based servers used to start the application.</td>
    </tr>
    <tr>
      <td>db/</td>
      <td>Contains your current database schema, as well as the database migrations.</td>
    </tr>
    <tr>
      <td>Gemfile<br>Gemfile.lock</td>
      <td>These files allow you to specify what gem dependencies are needed for your Rails application. These files are used by the Bundler gem. For more information about Bundler, see <a href="http://gembundler.com">the Bundler website</a>
      </td>
    </tr>
    <tr>
      <td>lib/</td>
      <td>Extended modules for your application.</td>
    </tr>
    <tr>
      <td>log/</td>
      <td>Application log files.</td>
    </tr>
    <tr>
      <td>public/</td>
      <td>The only folder seen to the world as-is. Contains the static files and compiled assets.</td>
    </tr>
    <tr>
      <td>Rakefile</td>
      <td>This file locates and loads tasks that can be run from the command line. The task definitions are defined throughout the components of Rails. Rather than changing Rakefile, you should add your own tasks by adding files to the lib/tasks directory of your application.</td>
    </tr>
    <tr>
      <td>README.rdoc</td>
      <td>This is a brief instruction manual for your application. You should edit this file to tell others what your application does, how to set it up, and so on.</td>
    </tr>
    <tr>
      <td>test/</td>
      <td>Unit tests, fixtures, and other test apparatus. These are covered in <a href="testing.html">Testing Rails Applications</a>
      </td>
    </tr>
    <tr>
      <td>tmp/</td>
      <td>Temporary files (like cache, pid and session files)</td>
    </tr>
    <tr>
      <td>vendor/</td>
      <td>A place for all third-party code. In a typical Rails application, this includes Ruby Gems and the Rails source code (if you optionally install it into your project).</td>
    </tr>
  </tbody>
</table>

!SLIDE bullets small
## Génération de code (1/3)

    @@@ sh
    rails generate scaffold task title:string completed:boolean

![scaffold](scaffold.png)

!SLIDE bullets
## Génération de code (2/3)
Rafraîchir le browser 

Oups... :-(

!SLIDE bullets small
## Génération de code (3/3)

- La solution est dans les message d'erreur

        @@@ Ruby
        $ rake db:migrate RAILS_ENV=development

- Rafraîchir le browser: [localhost:3000/tasks](localhost:3000/tasks)
  :-)

        @@@ Ruby
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


!SLIDE bullets small
## CRUD RESTful

Dans le fichier `config/routes.rb` :
    @@@ ruby
    # ...
    resources :tasks
    # ...

Dans la console exécuter `rake routes` :

    @@@ sh
          Prefix Verb   URI Pattern               Controller#Action
        tasks GET    /tasks(.:format)             tasks#index
              POST   /tasks(.:format)             tasks#create
     new_task GET    /tasks/new(.:format)         tasks#new
    edit_task GET    /tasks/:id/edit(.:format)    tasks#edit
         task GET    /tasks/:id(.:format)         tasks#show
              PATCH  /tasks/:id(.:format)         tasks#update
              PUT    /tasks/:id(.:format)         tasks#update
              DELETE /tasks/:id(.:format)         tasks#destroy

Indice: `tasks#index` → `TaskController.index`


!SLIDE bullets small
## CRUD RESTful

Editons `app/controller/tasks_controller.rb` :

    @@@ Ruby 
    # GET /tasks
    # GET /tasks.json
    def index
      @tasks = Task.all
    end

Par convention, la vue sera `app/views/tasks/index.html.erb`

    @@@ html
    <!-- ... -->
    <% @tasks.each do |task| %>
      <tr>
        <td><%= task.title %></td>
        <!-- ... -->
        <td><%= link_to 'Edit', edit_task_path(task) %></td>
        <!-- ... -->
    <% end %>
    <!-- ... -->

!SLIDE bullets small
## CRUD RESTful

## Faisons pointer la racine sur la liste des tâches

Dans `config/routes.rb` ajoutons la ligne suivante entre `do` et
`end`:

    @@@ Ruby
    root "tasks#index"

!SLIDE bullets small
## CRUD RESTful

Pour mieux comprendre les interactions entre les vues et les
controlleur ajoutons l'affichage des paramètres de la requête 
dans le fichier `app/views/layout/application.html.erb`:

    @@@ html
    <body>
      <%= yield %>
      <%= debug(params) if Rails.env.development? %>
    </body>

!SLIDE subsection
# La tête dans les nuages

## Déploiement sous Heroku

!SLIDE bullets small
## Informations sur le processus

[https://devcenter.heroku.com/articles/quickstart](https://devcenter.heroku.com/articles/quickstart)

[https://devcenter.heroku.com/articles/getting-started-with-rails4](https://devcenter.heroku.com/articles/quickstart)

!SLIDE bullets small
# Utilisation de PostgreSQL en production

Ajouter dans le fichier `Gemfile` les lignes suivantes :

    @@@ Ruby 
    ruby "1.9.3"

    group :production do
        gem 'pg', '0.15.1'
        gem 'rails_12factor', '0.0.2'
    end

et remplacer la ligne :

    @@@ Ruby
    gem 'sqlite3'

par :

    @@@ Ruby
    group :development, :test do
        gem 'sqlite3'
    end


!SLIDE bullets small
## La config db
.notes TODO: verify it's working
# modifier la config db

Modifier config/database.yml

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
## Installer les dépendances

    @@@ sh
    $ bundle install


!SLIDE bullets small
## Initialiser un repo git

    @@@ sh 
    $ git init
    $ git add .
    $ git commit -m "init"


!SLIDE bullets small
## Créer l'app sur heroku
    
    @@@ sh 
    $ heroku create

On déploit

    @@@ sh
    $ git push heroku master
    $ heroku run rake db:migrate
    $ heroku open

Enjoy : )

!SLIDE bullets small
# Make it nice (bootstrap)

Rajout au gemfile

    @@@ Ruby
    !SLIDE bullets
    gem 'bootstrap-sass', '2.3.2.0'

Installer (relancer le serveur après)
    
    @@@ sh
    $ bundle install

!SLIDE bullets small
## Configurer l'asset pipeline

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
## Créer un css sur mesure 
Créer app/assets/stylesheets/custom.css.scss

    @@@ css
    @import "bootstrap";

    body {
      margin: 20pt;
    }

!SLIDE bullets small
## Container

Appliquer la classe "container" à notre contenu.  
Editer app/views/layout/application.html.erb

    @@@ html
    <div class="container">
      <%= yield %>
    </div>


!SLIDE bullets small
## Partiels (création d'un 'header')

Créer le fichier app/views/layouts/_header.html.erb
    
    @@@ html
    <header class="navbar navbar-fixed-top navbar-inverse">
      <div class="navbar-inner">
        <div class="container">
          <%= link_to "JustDidIt", root_path, id: "logo" %>
        </div>
      </div>
    </header>

!SLIDE bullets small
## Intégrons le partiel dans le layout

Rajouter  <%= render 'layouts/header' %> dans layout

    @@@ html
    # app/views/layouts/application.html.erb
    <body>
      <%= render 'layouts/header' %>
      <div class="container">
        <%= yield %>
      </div>
    </body>

Rafraîchir le browser, 

meh...

!SLIDE bullets small
## Essayons d'améliorer un peu

Editer app/assets/stylesheets/custom.css.scss
    
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

    body {
      margin: 20pt;
      padding-top: 30px;
    }

!SLIDE bullets small
## Et encore...
Rajouter la classe "table" au tag table dans app/views/tasks/index.html.erb



!SLIDE bullets small
## Exercices

Rajouter un footer ?
Styler les <a> en bouttons ?


!SLIDE bullets small
# M dans mvc

Rajoutons une notion de Projet.  


Chaque tâche sera associée à un projet.


"Out of the box", Rails utilise le pattern ActiveRecord (Martin Fowler, 'Patterns of Enterprise Application Architecture')

!SLIDE bullets small
## Créons le modèle "Project".

    @@@ sh
    rails generate model project title:string \
    completed:boolean due_date:date

La classe résultante est minimale. (app/models/task.rb)


    @@@ Ruby
    class Task < ActiveRecord::Base
    end

Une migration est cependant nécéssaire.

    @@@ sh
    rake db:migrate

Par convention un objet Project sera lié à une table Projects.  
La table injecte ses colonnes au modèle.


!SLIDE bullets small
## Associations

    @@@ Ruby
    belongs_to     
    has_one    
    has_many    
    has_many :through    
    has_one :through    
    has_and_belongs_to_many   

[http://guides.rubyonrails.org/association_basics.html](http://guides.rubyonrails.org/association_basics.html)


!SLIDE bullets small
## Associations 

Exemple

    @@@ Ruby
    class Order < ActiveRecord::Base
      belongs_to :customer
    end

![belongs to](belongs_to.png)


!SLIDE bullets small
## Associations

On souhaite associer chaque Tâche à un Projet.

    @@@ Ruby
    #app/models/task.rb
    class Task < ActiveRecord::Base
      belongs_to :project
    end



!SLIDE bullets small
## Rails console (le 'repl' de rails)
    
    @@@ sh
    rails console   
    rails console --sandbox (rollback des écritures db à la sortie)


    p = Project.new
    p.title = "eat pizza"
    p = Project.new(title: "eat pizza", due_date: Date.today)
    p.save
    t = Task.new
    t.project
    t.project = p
    t.project
    t.save

Oups...

!SLIDE bullets small
# Et si on testait?


    @@@ Ruby
    # test/models/taks_test.rb
    class TaskTest < ActiveSupport::TestCase
      test "add project to task" do
        p = Project.new(title: 'my project')
        p.save
        t = Task.new(title: 'do it')
        t.project = p
        t.save

        assert Task.find_by_project_id(p.id).project_id == p.id
      end
    end

Lançons les tests

    @@@ sh
    rake test


!SLIDE bullets small
## De rouge à vert
Rajoutons une colonne project_id à la table Task

Créons une nouvelle migration

    @@@ sh
    $ rails g migration add_project_id_to_task project_id:integer  

Editons la migration (db/migration/...)

    @@@ Ruby
    # db/migrations/timestamp_add_project_id_to_task
    class AddProjectIdToTask < ActiveRecord::Migration
      def change
        add_column :tasks, :project_id, :integer
      end
    end

Lançons la migration

    @@@ sh
    $ rake db:migrate  


!SLIDE bullets small
# Test d'intégration
## Test d'intégration de la sélection du projet

On utilise https://github.com/jnicklas/capybara  


"Capybara helps you test web applications by simulating how a real user would interact with your app. It is agnostic about the driver running your tests and comes with Rack::Test and Selenium support built in."

!SLIDE bullets small
## Setup Capybara  
Rajouter au gemfile
    
    @@@ Ruby
    gem 'capybara', '2.1.0'

Installation des dépendances

    @@@ sh
    bundle install

Finalement, écrivons un helper pour intégrer capybara.

    @@@ Ruby
    # test/test_helper.rb
    class ActionDispatch::IntegrationTest
      # Make the Capybara DSL available in all integration tests
      include Capybara::DSL
    end



!SLIDE bullets small
## Le test

Générons le fichier de test...

    @@@ sh
    $ rails generate integration_test task_flows


Editons le fichier nouvellement crée.

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

!SLIDE small
## Faisons passer le test:

Editons app/views/tasks/_form.html.erb

    @@@ html
    <div class="field">
      <%= f.label :project %><br>
      <%= f.select :project_id, \
      Project.all.map {|p| [p.title, p.id]} %>
    </div>

!SLIDE small
## Le test nous permet un refactor

On aimerait limiter les projets aux projets en cours
    
Changeons le formulaire,

    @@@ html
    <div class="field">
      <%= f.label :project %><br>
      <%= f.select :project_id, @projects.map {|p| [p.title, p.id]}%>
    </div>

le controlleur,
    
    @@@ Ruby
    # GET /tasks/new
    def new
      @task = Task.new
      @projects = Project.all
    end

    # GET /tasks/1/edit
    def edit
      @projects = Project.ongoing
    end

!SLIDE small
## Le test nous permet un refactor

et finalement le modèle.

    @@@ Ruby
    class Project < ActiveRecord::Base
      scope :ongoing, \
          ->() { where("due_date > ? or due_date is null", Time.now) }
    end



!SLIDE small
## Exercices:
- Ecrire le test d'intégration pour la Project.ongoing
- Rajouter le nom du projet dans la liste des tâches (éditer app/views/tasks/index.html.erb)
- Lister les tâches par projet?

!SLIDE bullets small
# limite des générateurs.

on aimerait que les nouvelles tâches soit incomplète par défaut
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

      @@@ sh
      rails g migration add_default_value_to_completed_attribute

!SLIDE bullet small
# suite

    @@@ Ruby
    #db/migrate/timestamp_add_default_value_to_completed_attribute

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
    # app/views/tasks/_form.html.erb

    <div class="field">
      <%= f.label :completed %><br>
      <%= f.check_box :completed %>
    </div>

!SLIDE bullets small
# Exercices

- rediriger new task sucess sur la home


!SLIDE bullets small
## mark as completed
permettre à l'utilisateur de marquer une tâche comme complète depuis la vue index
(éviter le passage par la vue edit).  

Au passage enlever le boutton "show" qui n'a pas beaucoup de sens.

!SLIDE bullets small
## le test

    @@@ Ruby
    # test/integration/task_flows_test.rb
    test "task index should allow user to mark task as done" do
      visit(tasks_path)
      assert page.has_link?('I did it')
    end

$ rake test

!SLIDE bullets small
## la route

    @@@ ruby
    # routes.rb
    patch 'tasks/mark_completed/:id' => "tasks#mark_completed", \
                                  as: :mark_task_completed 

$ rake test

!SLIDE bullets small
## la vue

    @@@ ruby
    # app/views/tasks/index.html.erb
    <td><%= link_to 'I did it', mark_task_completed_path(task), \
    method: :patch %></td>

    <td><%= link_to 'Edit', edit_task_path(task) %></td>

    <td><%= link_to 'Destroy', task, method: :delete, \
     data: { confirm: 'Are you sure?' } %></td>

$ rake test

!SLIDE bullets small
## le test

    @@@ ruby
    test "task index should allow user to mark task as done" do
      task_title = "test completion"
      t = Task.create(title: task_title)
      visit(tasks_path)
      assert page.has_link?('I did it') 
      dom_element = all(:css, 'tr').last
      within(dom_element) do
          click_on('I did it')
      end
      updated_dom_element = all(:css, 'tr').last
      assert updated_dom_element.has_content?("true")
    end


$ rake test

!SLIDE bullets small
## le controlleur

    @@@ ruby
    before_action :set_task, only: [:show, :edit, :update,\
                                   :destroy, :mark_completed]
    
     ...

    def mark_completed
      respond_to do |format|
        if @task.mark_as_completed
          format.html { redirect_to tasks_path, notice: 'Task was successfully updated.' }
          format.json { head :no_content }
        else
          format.html { redirect_to tasks_path, notice: 'the task has not been marked complete' }
          format.json { render json: @task.errors, status: :unprocessable_entity }
        end
      end
    end

$ rake test

!SLIDE bullets small
## le modèle

    @@@ ruby
    class Task < ActiveRecord::Base
    belongs_to :project
      def mark_as_completed
        update(completed: true)
      end
    end

$ rake test

!SLIDE small
# Exercices

- Que se passe-t'il si une tâche est déjà complète (ne pas afficher le boutton dans ce cas par ex.)

!SLIDE bullets small
# Egocentric Todo
- On aimerait tweeter chaque tâche accomplie sur le compte twitter @softshake_ruby
- [https://twitter.com/softshake_ruby](https://twitter.com/softshake_ruby)

!SLIDE bullets small
# Utilisation de la gem _twitter_

- Nous allons utiliser la gen _twitter_ disponible à l'adresse [https://github.com/sferik/twitter](https://github.com/sferik/twitter)
- Ajouter la ligne suivante dans le fichier _Gemfile_ :

        @@@ Ruby
        gem 'twitter'

- Exécuter :

        @@@ sh
        bundle install

!SLIDE bullets small
# Configuration du compte Twitter

Créer le fichier `config/initializers/twitter_tokens.rb` avec le
contenu suivant :

        @@@ Ruby
        Todo::Application.config.twitter_secrets = {
          consumer_key:  "WWhRtarQA1kXduLbNILU4w"
          consumer_secret:
            "DXT4kTWndpzco3imwYEhDI9YQuo9k9RBLykmRnCM"
          oauth_token:
            "1960777086-t8uRh2v8Dpdz4W6DgW2NVeeXF3YbaiTOh0OxqMW"
          oauth_token_secret:
            "SMIpGweHdz0Bzlbz7odZKluqEadaTsGnT1qVfZrF86Q"
        }

- Les instruction sont disponibles sur le site de Twitter [https://dev.twitter.com/apps/new](https://dev.twitter.com/apps/new)


!SLIDE bullets small
## Le service twitter

- Créer un fichier `lib/twitter_service.rb` avec le contenu suivant :

        @@@ Ruby
        require 'twitter'

        # needed on windows
        OpenSSL::SSL::VERIFY_PEER = OpenSSL::SSL::VERIFY_NONE

        # one user global config
        Twitter.configure do |config|
          secrets = Todo::Application.config.twitter_secrets
          config.consumer_key = secrets[:consumer_key]
          config.consumer_secret = secrets[:consumer_secret]
          config.oauth_token = secrets[:oauth_token]
          config.oauth_token_secret = secrets[:oauth_token_secret]
        end

        module TwitterService
          def self.tweet_task(task, client=Twitter)
            client.update("I did it #{Time.now}: #{task.title}")
          end
        end

!SLIDE bullets small
## Auto chargement du répertoire lib

    @@@ Ruby
    # config/application.rb
    class Application < Rails::Application
      ...

      config.autoload_paths += Dir["#{config.root}/lib/**/"]

      ...
    end

!SLIDE bullets small
## Un test

    @@@ Ruby
    # test/lib/twitter_service_test
    require 'test_helper'

    class MockTwitterClient
      attr_reader :update_call_count
      attr_reader :message

      def initialize
        @update_call_count = 0
      end

      def update(message)
        @message = message
        @update_call_count += 1
      end
    end

    class TwitterServiceTest < ActiveSupport::TestCase
      test "#tweet_task, calls Twitter client's update method with \
                  message containing task.title" do
        task_title = "some text"
        task = Task.new(title: task_title)
        twitter_client = MockTwitterClient.new

        TwitterService.tweet_task(task, twitter_client)

        assert twitter_client.message.include? task_title
        assert_equal twitter_client.update_call_count, 1
      end
    end

!SLIDE bullets small
## Pour exécuter le test

    @@@ sh
    rake test TEST=test/lib/twitter_service_test.rb

ou

    @@@ sh
    rake test:all

!SLIDE bullets small
## Finalement, on utilise le service dans le controlleur

    @@@ Ruby
    def mark_completed
      respond_to do |format|
        if @task.mark_as_completed
          TwitterService.tweet_task @task
          format.html { redirect_to tasks_path, notice: 'Task was successfully updated.' }
          format.json { head :no_content }
        else
          ...
        end
      end
    end

!SLIDE small
## Exercices

- Ajouter un 'flash message' pour indiquer à l'utilisateur que le tweet est parti

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


