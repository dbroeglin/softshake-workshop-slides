!SLIDE subsection
# 

!SLIDE bullets small
# Le M dans MVC

- Ajoutons une notion de Projet. Chaque tâche sera 
associée à un projet.
- ![Task-Project class diagram](class_diagram.png)


Rails utilise le patron de conception (_design pattern_)
  ActiveRecord (cf. _Patterns of Enterprise Application Architecture_
  par Martin Fowler)

!SLIDE bullets small
## Ajout d'un modèle _Project_

    @@@ sh
    rails generate model project title:string \
      completed:boolean due_date:date

- La commande génère le, plutôt minimaliste, fichier
`app/models/project.rb` :

        @@@ Ruby
        class Project < ActiveRecord::Base
        end

- Une migration de la base de données est nécéssaire :

        @@@ sh
        rake db:migrate

Par convention un objet Project sera lié à une table Projects.  
La table injecte ses colonnes au modèle.


!SLIDE bullets small
## Associations (1/2)

- Rails dispose de différents types d'associations :

          @@@ Ruby
        class Task < ActiveRecord::Base

          belongs_to :project

          has_one :acceptance

          has_many :time_entry

          has_and_belongs_to_many :tag

        end

Cf. [http://guides.rubyonrails.org/association_basics.html](http://guides.rubyonrails.org/association_basics.html)

!SLIDE bullets small
## Associations (2/2)

- ![Task-Project class diagram](class_diagram.png)
-  Pour associer chaque Tâche à un Projet modifier le fichier
   `app/models/task.rb` :

        @@@ Ruby
        class Task < ActiveRecord::Base
          belongs_to :project
        end


!SLIDE bullets small
## Rails console (le _REPL_ de Rails)

- Pour lancer la console :

        @@@ sh
        rails console
        rails console --sandbox # rollback à la sortie

- Tapons:

        @@@ Ruby
        p = Project.new
        p.title = "eat pizza"
        p = Project.new(title: "eat pizza", due_date: Date.today)
        p.save
        t = Task.new
        t.project
        t.project = p
        t.project
        t.save

- Oups...

!SLIDE bullets small
# Et si on testait ?

- Editer le fichier `test/models/taks_test.rb` :

        @@@ Ruby
        class TaskTest < ActiveSupport::TestCase
          test "add project to task" do
            task = Task.new(title: 'do it')
            task.project = projects(:one)
            task.save

            assert Task.find_by_project_id(projects(:one).id) == task
          end
        end

- Lancer les tests :

        @@@ sh
        rake test

!SLIDE bullets smaller
## De rouge à vert

- Ajoutons une colonne project_id à la table Task.

- Créer une nouvelle migration :

        @@@ sh
        rails g migration add_project_id_to_task project_id:integer

- Editer `db/migrate/*_add_project_id_to_task.rb` :

        @@@ Ruby
        class AddProjectIdToTask < ActiveRecord::Migration
          def change
            add_column :tasks, :project_id, :integer
          end
        end

- Lancer la migration :

        @@@ sh
        rake db:migrate


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
