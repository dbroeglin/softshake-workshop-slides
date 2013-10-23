!SLIDE bullets small
# Esthétique (bootstrap)

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

