!SLIDE subsection
# La tête dans les nuages

## Déploiement sous Heroku

!SLIDE bullets small
## Informations sur le processus

[https://devcenter.heroku.com/articles/quickstart](https://devcenter.heroku.com/articles/quickstart)

[https://devcenter.heroku.com/articles/getting-started-with-rails4](https://devcenter.heroku.com/articles/quickstart)

!SLIDE bullets small
## Utilisation de PostgreSQL en production

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

Exécuter :

    @@@ sh
    $ bundle install

!SLIDE bullets small
.notes TODO: verify it's working
## Adapter la config db

- Modifier `config/database.yml`. La section :

        @@@ Ruby
        production:
          adapter: sqlite3
          database: db/production.sqlite3
          pool: 5
          timeout: 5000

- devient:

        @@@ Ruby
        production:
          adapter: postgresql
          encoding: unicode
          database: myapp_production
          pool: 5
          username: myapp
          password:


!SLIDE bullets smaller
## Déploiement

- Créer un référentiel Git :

        @@@ sh
        git init
        git add .
        git commit -m "init"


- Créer l'app sur heroku :

        @@@ sh
        heroku create

- Déployer :

        @@@ sh
        git push heroku master
        heroku run rake db:migrate
        heroku open
