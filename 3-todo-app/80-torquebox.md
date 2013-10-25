!SLIDE subsection
.notes live coding
# Java: Deploiement dans Torquebox

!SLIDE bullets
## JRuby

- Interpréteur Ruby écrit en Java
- Ruby tourne dans la JVM
  - Accès à Java depuis Ruby
  - Accès à Ruby depuis Java

- Défauts :
  -  pas de `fork()`
  -  certaines extensions `C` ne marchent pas

!SLIDE bullets
## Torquebox

- Permet de déployer une application _Rack_ sous JBoss AS
- Met à disposition de Rails les services JBoss :
  - Messages (_Messaging_)
  - Planification (_Scheduling_)
  - Services (application Ruby quelconque)
  - Cache Infinispan
  - Cluster
  - Web sockets
  - Toute librairie Java/Scala/Clojure, etc.

!SLIDE bullets
## Installation de Torquebox

- Installer JRuby (version 1.7.5 par exemple)

- Exécuter :

        @@@ sh
        gem install torquebox-server

!SLIDE bullets small
## Adapatation pour Torquebox

- Exécuter :

        @@@ sh
        torquebox rails

- Editer `Gemfile` et remplacer la ligne :

        @@@ Ruby
        gem 'sqlite3'
- par :

        @@@ Ruby
        gem 'activerecord-jdbcsqlite3-adapter'

!SLIDE bullets small
## Déploiement sous Torquebox

- Dans une *autre* console lancer : 

        @@@ sh
        torquebox run

- Effacer le fichier `Gemfile.lock`.
- Exécuter :

        @@@ sh
        bundle install
        torquebox deploy

- [http://localhost:8080](http://localhost:8080) !!!


