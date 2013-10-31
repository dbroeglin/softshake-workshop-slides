!SLIDE subsection
# Test _driven_

!SLIDE bullets small
# Le M dans MVC

- Ajoutons une notion de Projet. Chaque tâche sera 
associée à un projet.

![Task-Project class diagram](/slides/3-todo-app/class_diagram.png)

Rails utilise le patron de conception (_design pattern_)
  ActiveRecord (cf. _Patterns of Enterprise Application Architecture_
  par Martin Fowler)

!SLIDE bullets small
## Ajout d'un modèle _Project_

```bash
rails generate model project title:string \
  completed:boolean due_date:date
```

- La commande génère le, plutôt minimaliste, fichier
`app/models/project.rb` :

```ruby
class Project < ActiveRecord::Base
end
```

- Une migration de la base de données est nécéssaire :

```bash
rake db:migrate
```

Par convention un objet Project sera lié à une table Projects.  
La table injecte ses colonnes au modèle.

!SLIDE bullets small
## Associations (1/2)

- Rails dispose de différents types d'associations :

```ruby
class Task < ActiveRecord::Base

  belongs_to :project

  has_one :acceptance

  has_many :time_entry

  has_and_belongs_to_many :tag

end
```

Cf. [http://guides.rubyonrails.org/association_basics.html](http://guides.rubyonrails.org/association_basics.html)

!SLIDE bullets small
## Associations (2/2)

![Task-Project class diagram](/slides/3-todo-app/class_diagram.png)

-  Pour associer chaque Tâche à un Projet modifier le fichier
   `app/models/task.rb` :

```ruby
class Task < ActiveRecord::Base
  belongs_to :project
end
```

!SLIDE bullets small
## Rails console (le _REPL_ de Rails)

- Pour lancer la console :

```bash
rails console
rails console --sandbox # rollback à la sortie
```

- Tapons :

```ruby
p = Project.new
p.title = "eat pizza"
p = Project.new(title: "eat pizza", due_date: Date.today)
p.save
t = Task.new
t.project
t.project = p
```

- Oups...

!SLIDE bullets small
# Et si on testait ?

- Editer le fichier `test/models/task_test.rb` :

```ruby
class TaskTest < ActiveSupport::TestCase
  test "add project to task" do
    task = Task.new(title: 'do it')
    task.project = projects(:one)
    task.save

    assert Task.find_by_project_id(projects(:one).id) == task
  end
end
```
- Lancer les tests :

```bash
rake test
```

!SLIDE bullets smaller
## De rouge à vert

- Ajoutons une colonne `project_id` à la table `TASK`.
- Créer une nouvelle migration :

```bash
rails g migration add_project_id_to_task project_id:integer
```

- Editer `db/migrate/*_add_project_id_to_task.rb` :

```ruby
class AddProjectIdToTask < ActiveRecord::Migration
  def change
    add_column :tasks, :project_id, :integer
  end
end
```

- Lancer la migration :

```bash
rake db:migrate
```

!SLIDE bullets small
## Test d'intégration

- Nous allons utiliser Capybara un framework qui :
  - Utilise Selenium
  - Simule les interactions d'un utilisateur réèl
  - Fournit un DSL simplifié

Cf. https://github.com/jnicklas/capybara  

!SLIDE bullets small
## Installation de Capybara

- Ajouter au fichier `Gemfile` :

```ruby
gem 'capybara', '2.1.0'
```

- Installer la gem :

```bash
bundle install
```

- Ajouter à la fin du fichier `test/test_helper.rb` :

```ruby
require 'capybara/rails'
class ActionDispatch::IntegrationTest
  include Capybara::DSL
end
```

!SLIDE bullets small
.notes Expliquer le test d'intégration. Expliquer Selenium si nécéssaire
## Un test d'intégration

- Générer le fichier de test :

```bash
rails generate integration_test task_flows
```

- Editer le fichier `test/integration/task_flows_test.rb` :

```ruby
class TaskFlowsTest < ActionDispatch::IntegrationTest
  test "new task creation has project selection" do
    p = projects(:one)

    visit(new_task_path())
    assert page.has_content?('New task')
    within("select[name='task[project_id]']") do
        option = find("option[value='#{p.id}']")
        assert_not option.nil?
        assert_equal p.title, option.text
    end
  end
end
```

!SLIDE bullets small
## Faisons passer le test

- Editons app/views/tasks/_form.html.erb

```html
<div class="field">
  <%= f.label :project %>
  <%= f.select :project_id,
        Project.all.map {|p| [p.title, p.id]} %>
</div>
```

- Exécuter `rails console` et entrer :

```ruby
Project.create(title: "Learn Rails", 
    due_date: Date.today + 1)
Project.create(title: "Master Rails")
```

!SLIDE bullets small
## Le test nous permet un _refactoring_ (1/2)

On aimerait limiter les projets aux projets en cours.

- Modifier le form `app/views/tasks/_form.html.erb` :

```html
<div class="field">
  <%= f.label :project %><br>
  <%= f.select :project_id, 
        Project.ongoing.map {|p| [p.title, p.id]} %>
</div>
```

- et le modèle `app/models/project.rb` :

```ruby
class Project < ActiveRecord::Base
  scope :ongoing, ->() do
    where("due_date > ? or due_date is null", Time.now) 
  end
end
```

!SLIDE bullets small
## Le test nous permet un _refactoring_ (2/2)

- Lancer les tests :

```bash
rake test
```

- Oups...
- Editer `test/fixtures/projects.yml` et supprimer `due_date` 
pour `one` :

```bash
one:
  title: MyString
  completed: false
```

!SLIDE bullet small
## Exercices:
1. Ecrire le test d'intégration pour la méthode `Project.ongoing`.
2. Ajouter le nom du projet dans la liste des tâches (éditer
  `app/views/tasks/index.html.erb`).
3. Lister les tâches par projet.

!SLIDE bullets small
# Evolution du modèle (1/2)

On aimerait que les nouvelles tâches soit incomplètes par défaut,
i.e. que `Task.new.completed` vaille `false` au lieu de `NULL`.

- Ajouter un test dans `test/models/task_test.rb` :

```ruby
test "a new task should not be completed" do
  t = Task.new
  assert t.completed == false
end
```

- Lancer les tests :

```bash
rake test
```

!SLIDE bullets smaller
# Evolution du modèle (2/2)

- Générer une nouvelle migration :

```bash
rails g migration add_default_value_to_completed
```

- Editer `db/migrate/*_add_default_value_to_completed.rb` :

```ruby
class AddDefaultValueToCompleted < ActiveRecord::Migration
  def up
      change_column :tasks, :completed, :boolean, :default => false
  end

  def down
      change_column :tasks, :completed, :boolean, :default => nil
  end
end
```

- Lancer la migration :

```bash
rake db:migrate
```

!SLIDE bullets
# Exercices

1. Rediriger l'utilisateur sur la page de garde en cas de succès.
2. Supprimer l'édition de la complétion de la vue d'édition.

!SLIDE bullets small
## «Faciliter la tâche»

Permettre à l'utilisateur de marquer une tâche comme complète depuis 
la liste des tâches.

Au passage, enlever le boutton "show" qui n'a pas beaucoup de sens.

- Ajouter un test dans `test/integration/task_flows_test.rb`

```ruby
test "user can mark a task as done from the task list" do
  visit(tasks_path)
  within(first(:css, 'tbody tr')) do
    assert has_content?('false'), 'should have false'
    assert has_link?('I did it'), 'should have link'
  end
end
```

- Lancer les tests :

```bash
rake test
```

!SLIDE bullets small
## «Faciliter la tâche»

- Modifier la vue `app/views/tasks/index.html.erb` en ajoutant :

```html
<td>
  <%= link_to 'I did it', complete_task_path(task),
        method: :patch %>
</td>
```

- et en supprimant :

```html
<td><%= link_to 'Show', task %></td>
```

- Lancer les tests :

```bash
rake test
```

!SLIDE bullets small
## «Faciliter la tâche»

- Editer `config/routes.rb` :

```bash
patch 'tasks/complete/:id' => "tasks#complete", \
  as: :complete_task
```

- Lancer les tests :

```bash
rake test
```

!SLIDE bullets small
## «Faciliter la tâche»

- Ajouter un test dans `test/integration/task_flows_test.rb`

```ruby
test "user can mark a task as done from the task list" do
  visit(tasks_path)
  within(first(:css, 'tbody tr')) do
    assert has_content?('false'), "should have false"
    click_on 'I did it'
  end
  within(first(:css, 'tbody tr')) do
    assert has_content?('true'), "should have true"
  end
end
```

- Lancer les tests :

```bash
rake test
```

!SLIDE bullets smaller
## «Faciliter la tâche»

- Editer le fichier `app/controllers/tasks_controller.rb` :

```ruby
before_action :set_task, only: [:show, :edit, :update,
                               :destroy, :complete]
# ...
def complete
  respond_to do |format|
    if @task.complete
      format.html { 
        redirect_to tasks_path, 
          notice: 'Task was successfully updated.' }
      format.json { head :no_content }
    else
      format.html { 
        redirect_to tasks_path, 
          notice: 'the task has not been marked complete' }
      format.json { render json: @task.errors, 
                      status: :unprocessable_entity }
    end
  end
end
```

- Lancer les tests :

```bash
rake test
```

!SLIDE bullets small
## «Faciliter la tâche»

- Editer le modèle `app/models/task.rb` et ajouter la méthode :

```ruby
class Task < ActiveRecord::Base
  belongs_to :project

  def complete
    update(completed: true)
  end
end
```

- Lancer les tests :

```bash
rake test
```

!SLIDE bullets small
# Exercices

1. Que se passe-t'il si une tâche est déjà complète
(ne pas afficher le lien dans ce cas par exemple)

