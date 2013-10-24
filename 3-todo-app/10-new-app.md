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
- Pour relancer le serveur taper `CTRL + C` (`CMD + C` sous MacOSX) puis:

        @@@ sh
        rails server

!SLIDE bullets small
.notes TODO: translate 
## Structure d'un projet rails

`app/` : contient le code de l'application : vues, modèles, contrôlleurs, etc.

`config/` : contient la configuration de l'application : routes, credentials, etc.

`db/` : contient les fichiers de la base de donnée SQLite.

`Gemfile` : spécifie les dépendances utilisées par l'application.

`public` : contient les resources web statiques de l'application.

`Rakefile` : spécifie les tâches de construction automatisée de l'application.

`test` : contient le code de test de l'application.

Source: [http://guides.rubyonrails.org/getting_started.html#creating-the-blog-application](http://guides.rubyonrails.org/getting_started.html#creating-the-blog-application)

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

- La solution est dans les messages d'erreur

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
contrôlleurs, ajoutons l'affichage des paramètres de la requête 
dans le fichier `app/views/layout/application.html.erb`:

    @@@ html
    <body>
      <%= yield %>
      <%= debug(params) if Rails.env.development? %>
    </body>
