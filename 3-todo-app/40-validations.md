!SLIDE subsection
# Validation des entrées

!SLIDE bullets small
## Validations

- Elles ont lieu au moment de la persistence.
- Les méthodes qui lancent les validations sont :

        @@@ Ruby
        create
        create!
        save
        save!
        update
        update!

Les versions avec '!' lancent une exception en cas de problème.  
Les autres retournent 'false'.

!SLIDE bullets small
## Valider la présence du titre

- Ajouter `test/models/task_test.rb` dans le test :

        @@@ Ruby
        test "title should be present"  do
          t = Task.create

          assert_match(/can't be blank/, t.errors[:title].first)
        end

- Ajouter la ligne suivante dans le modèle 
`app/models/task.rb` :

        @@@ ruby
        class Task < ActiveRecord::Base
          validates :title, presence: true
          # ...
        end

!SLIDE bullets small
## Illustration dans la console Rails

- Lancer la console `rails console` et entrer :

        @@@ ruby
        t = Task.new
        t.errors # pas d'erreur encore
        t.valid? # exécution de la validation
        t.errors

        t = Task.create
        t.errors # des erreurs sont présentes

        t.save
        t.save!  # lance une exception

        t.errors[:title].any?


!SLIDE bullets small
## Tester la validation dans le browser, une correction

- Créer une nouvelle tâche sans titre. Oups: `@projects` est `nil`

- Réinitialiser la variable d'instance `@projects `

        @@@ Ruby
        def create
          @task = Task.new(task_params)

          respond_to do |format|
            if @task.save
              format.html { redirect_to @task,
                            notice: 'Task was successfully created.' }
              format.json { render action: 'show',
                            status: :created, location: @task }
            else
              @projects = Project.all # ligne à ajouter
              format.html { render action: 'new' }
              format.json { render json: @task.errors,
                            status: :unprocessable_entity }
            end
          end
        end

!SLIDE bullets small
## Exercices

1. Ajouter une validation sur la présence de `title` sur le 
modèle `Project`.
1. Ajouter une validation sur l'attribut `due_date` de `Project` qui
vérifie que le nouveau projet est dans le futur.  

Références: [http://guides.rubyonrails.org/active_record_validations.html](http://guides.rubyonrails.org/active_record_validations.html)
