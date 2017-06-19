# ConditionalDeploy

ConditionalDeploy - This gem does a conditional deploy, by comparing the currently deployed branch with the branch being deployed. This further checks if there is any files effected the the git repo tree. Deploys are aborted if no changes are found.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'conditional-deploy', git: 'git@github.com:ronakjain90/conditional-deploy.git'
```

And then execute:

    $ bundle

## Usage

1. In the Capfile include 
    
    ` require 'capistrano/conditional_deploy'`

## Explanation

This gem works by adding a hook into the existing deploy process. `deploy:starting` hooks is added that runs a set of rules immediately after the deploy is started.

##### Rules being run:

          currently_deployed_version = capture("cat #{current_path}/REVISION").strip rescue nil
          git = Git.open('../')
          current = git.object(currently_deployed_version)
          deploying = git.object("origin/#{fetch(:branch)}")
          git_difference = git.diff(current, deploying).stats
          files_changed = git_difference[:files].keys
          app_files_changed = files_changed.select{|m| m.include?("#{fetch(:repo_tree)}/")}.count
          if app_files_changed == 0
            abort "Nothing to Update!"
          end

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/weddingwire/conditional-deploy. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

