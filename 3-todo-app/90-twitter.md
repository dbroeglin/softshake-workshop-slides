!SLIDE bullets small
# Egocentric Todo
- On aimerait tweeter chaque tâche accomplie sur le compte twitter @softshake_ruby
- [https://twitter.com/softshake_ruby](https://twitter.com/softshake_ruby)

!SLIDE bullets small
# Utilisation de la gem _twitter_

- Nous allons utiliser la gen _twitter_ disponible à l'adresse [https://github.com/sferik/twitter](https://github.com/sferik/twitter)
- Ajouter la ligne suivante dans le fichier _Gemfile_ :

        @@@ Ruby
        gem 'twitter'

- Exécuter :

        @@@ sh
        bundle install

!SLIDE bullets small
# Configuration du compte Twitter

- Créer le fichier `config/initializers/twitter_tokens.rb` avec le
contenu suivant :

        @@@ Ruby
        Todo::Application.config.twitter_secrets = {
          consumer_key:  "WWhRtarQA1kXduLbNILU4w"
          consumer_secret:
            "DXT4kTWndpzco3imwYEhDI9YQuo9k9RBLykmRnCM"
          oauth_token:
            "1960777086-t8uRh2v8Dpdz4W6DgW2NVeeXF3YbaiTOh0OxqMW"
          oauth_token_secret:
            "SMIpGweHdz0Bzlbz7odZKluqEadaTsGnT1qVfZrF86Q"
        }

- Les instruction sont disponibles sur le site de Twitter [https://dev.twitter.com/apps/new](https://dev.twitter.com/apps/new)


!SLIDE bullets small
## Le service twitter

- Créer un fichier `app/models/twitter_service.rb` avec le contenu suivant :

        @@@ Ruby
        require 'twitter'

        # needed on windows
        OpenSSL::SSL::VERIFY_PEER = OpenSSL::SSL::VERIFY_NONE

        # one user global config
        Twitter.configure do |config|
          secrets = Todo::Application.config.twitter_secrets
          config.consumer_key = secrets[:consumer_key]
          config.consumer_secret = secrets[:consumer_secret]
          config.oauth_token = secrets[:oauth_token]
          config.oauth_token_secret = secrets[:oauth_token_secret]
        end

        module TwitterService
          def self.tweet_task(task, client=Twitter)
            client.update("I did '#{task.title}' at #{Time.now.to_s(:short)}")
          end
        end

!SLIDE bullets small
## Un test

    @@@ Ruby
    # test/lib/twitter_service_test
    require 'test_helper'

    class MockTwitterClient
      attr_reader :update_call_count
      attr_reader :message

      def initialize
        @update_call_count = 0
      end

      def update(message)
        @message = message
        @update_call_count += 1
      end
    end

    class TwitterServiceTest < ActiveSupport::TestCase
      test "#tweet_task, calls Twitter client's update method with \
                  message containing task.title" do
        task_title = "some text"
        task = Task.new(title: task_title)
        twitter_client = MockTwitterClient.new

        TwitterService.tweet_task(task, twitter_client)

        assert twitter_client.message.include? task_title
        assert_equal twitter_client.update_call_count, 1
      end
    end

!SLIDE bullets small
## Tweet à la sauvegarde

- Dans le modèle `app/models/tasks.rb` ajouter les lignes 
suivantes :

        @@@ Ruby
        after_save do
          if Rails.env.production?
            if completed
              TwitterService.tweet_task self
            end
          end
        end

