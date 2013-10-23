!SLIDE small
# Les validations
Elles ont lieu au moment de la persistence.  
Garder à l'esprit la distinction entre un objet en mémoire et l'état de la DB.   
Taper au terminal: rails c --sandbox 

    @@@ Ruby
    Task.new.new_record?  # non persisté
    Task.save
    Task.new.new_record?  # persisté

Garder la session 'rails console' ouverte pour la suite

!SLIDE bullets small
## Validations
Les méthodes qui lancent les validations avant la persistence db:

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
### Rajoutons une validation sur la présence du titre:

    @@@ ruby
    # app/models/task.rb

    class Task < ActiveRecord::Base
      belongs_to :project
      validates :title, presence: true
    end

!SLIDE bullets small
### Retour sur la console Rails

    @@@ ruby
    reload!

    t = Task.new
    t.errors
    t.valid?
    t.errors

    t = task.create
    t.errors

    t.save
    t.save!

    t.errors[:title].any?

!SLIDE bullets small
### Le test unitaire (a posteriori)

    @@@ Ruby
    # test/models/task_test.rb
    test "title should be present"  do
      t = Task.create
      expected_error = "can't be blank"
      assertion_message = %Q[errors[:title] does not \
                            contain "#{expected_error}"]

      assert t.errors[:title].include?(expected_error), \ 
                                            assertion_message
    end

!SLIDE small
## Tester la validation dans le browser

Créer une nouvelle tâche sans titre  
Oups @projects est nil

!SLIDE bullets small
## Tester la validation dans le browser, une correction

Réinitialiser la variable d'instance @projects

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
          @projects = Project.all
          format.html { render action: 'new' }
          format.json { render json: @task.errors, 
                        status: :unprocessable_entity }
        end
      end
    end

!SLIDE small
## Exercises

Références: [http://guides.rubyonrails.org/active_record_validations.html](http://guides.rubyonrails.org/active_record_validations.html)

1. Rajouter une validation sur la présence de 'title' sur le model Project  
1. Rajouter une validation sur l'attribut 'due date' de Project qui vérifie que le nouveau projet est dans le futur.  