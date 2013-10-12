# slides for a ruby on rails workshop 

the slides are generated with [showoff](https://github.com/schacon/showoff)

		gem install showoff
		cd softshake-workshop-slides
		showoff serve

## note 

At the date of the presentation (october 2013), the gem on rubygems was quite behind the development version. We encountered [issue](https://github.com/schacon/showoff/issues/192) .The fix is in the git repo. It might therefore be best to install manually.

		git clone https://github.com/schacon/showoff
		cd showoff
		bundle install
		gem build showoff.gemspec
		gem install showoff-x.x.x.gem