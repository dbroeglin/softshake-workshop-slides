!SLIDE subsection
# Esthétique

## Twitter Bootstrap

[http://getbootstrap.com/](http://getbootstrap.com/)

!SLIDE bullets
## Ajouter _bootstrap_
- Ajouter dans le fichier `Gemfile` :

        @@@ Ruby
        gem 'bootstrap-sass', '2.3.2.0'

- Installer la gem :

        @@@ sh
        bundle install

- Relancer le serveur.


!SLIDE bullets small
.notes TODO: is this one necessary ?
## Configurer l'asset pipeline

- Dans le fichier `config/application.rb` ajouter la ligne 
contenant `config.assets.precompile`

        @@@ Ruby
        # config/application.rb
        module Todo
          class Application < Rails::Application
            # ...
            config.assets.precompile += %w(*.png *.jpg *.jpeg *.gif)
            # ...
          end
        end

- Relancer le serveur.

!SLIDE bullets smaller
## Un CSS sur mesure

- Effacer `app/assets/stylesheets/scaffolds.css.scss`
- Ajouter dans `app/assets/stylesheets/tasks.css.scss` :

        @@@ css
        @import "bootstrap";

        form {
          @extend .form-inline;
        }

        label {
          @extend .input-small;
          font-weight: bold;
        }

        .field {
          margin: 10pt;
        }

        table {
          @extend .table;
        }


!SLIDE bullets smaller
## Conteneur et sauts de ligne

- Dans le fichier `app/views/layouts/application.html.erb` remplacer :

        @@@ html
        <%= yield %>

  par :

        @@@ html
        <div class="container">
          <%= yield %>
        </div>

- Dans le fichier `app/views/tasks/_form.html.erb` supprimer tous les tags
`<br>`.

!SLIDE bullets small
## Exercices

- Ajouter une barre de navigation bootstrap.
- Modifier le style des liens pour leur donner l'aspect d'un bouton.
