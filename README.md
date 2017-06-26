# Conditional Deploy

Have you ever tried to run a [microlith](http://techblog.thescore.com/2015/09/18/one-repo-to-rule-them-all/) repository that holds many different apps that share code in weird and wacky ways? Yeah, of course you have. Well, now you can actually attempt to write a single deploy script that can detect changes in each of your sub-apps and only deploy if they actually need to be. (ie: Consider you want to write a single `deploy` command that just "Does the Right Thing" ™ because you don't want to overwork [because overworking is bad], or you just have a moral issue having machines do more work than they need to...)

Add this Gem to your Capfile, and Capistrano will only actually execute the deployment command you just called if there are relevant changes to the app you're deploying. Cool beans.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'conditional-deploy', git: 'git@github.com:ienders/conditional-deploy.git'
```

And then execute:

    $ bundle

## Usage

1. Include it in your `Capfile`

```ruby
require 'capistrano/conditional_deploy'
```

2. Spec out your app's dependencies by specifying which subtrees of your repo you are dependent on. We use the `:repo_tree` setting by default, because you're obviously using that for a multi-app repo. If you have other stuff you'd like to depend on (say you have a shared gem in your repo that you also need to check for changes to), then just add an additional `:deploy_dependencies` array with more stuff. For example, in `config/deploy.rb`:

```ruby
set :repo_tree, 'sub-app'
set :deploy_dependencies, [ 'legacy-app/db/', 'foobar-biz-logic-gem/' ]
```

Now, your deploy will actually go through if any of the following folders/files are changed:

    sub-app/**
    legacy-app/db/**
    foobar-biz-logic-gem/**

You can leave off `:deploy_dependencies` if you only need to deploy what's under the `:repo_tree`.

## Forcing Deployments

Sometimes things go wrong, and maybe you just really need to deploy something, or restart a server with a Capistrano command that may not actually represent a change to your app. To bypass the checks and the abort that occurs when things don't show as dirty, just set a `FORCE` environment variable (any value is fine). For example:

    FORCE=1 cap production deploy

We'll get out of the way and let this thing go for it.

## Explanation

This gem works by adding a hook into the existing deploy process. `deploy:starting` hooks is added that runs a set of rules immediately after the deploy is started.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/ienders/conditional-deploy. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

