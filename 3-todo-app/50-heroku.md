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

