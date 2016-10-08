# For CircleCI
require 'bundler/setup'

# Style tests. Rubocop and Foodcritic
namespace :style do
  require 'rubocop/rake_task'
  require 'foodcritic'
  desc 'RuboCop'
  RuboCop::RakeTask.new(:ruby)

  desc 'FoodCritic'
  FoodCritic::Rake::LintTask.new(:chef) do |task|
    task.options = {
      fail_tags: ['correctness'],
      chef_version: '12.14.89',
      tags: %w(~FC001 ~FC019)
    }
  end
end

# Rspec and ChefSpec
namespace :unit do
  require 'coveralls/rake/task'
  require 'rspec/core/rake_task'
  desc 'Unit Tests (Rspec & ChefSpec)'
  RSpec::Core::RakeTask.new(:rspec)
end

# Integration Tests - Kitchen
namespace :integration do
  require 'kitchen'

  # Load Specific Kitchen Configuration YAML
  def load_kitchen_config(yaml)
    Kitchen.logger = Kitchen.default_file_logger
    kitchen_loader = Kitchen::Loader::YAML.new(local_config: yaml)
    Kitchen::Config.new(loader: kitchen_loader, log_level: :info)
  end

  # Run Each Test Instance in All Test Suites from YAML
  desc 'kitchen - docker - test'
  task :docker do
    load_kitchen_config('.kitchen.docker.yml').instances.each do |instance|
      instance.test(:always)
    end
  end

  # Run Each Test Instance in All Test Suites from YAML
  desc 'kitchen - ec2 - test'
  task :ec2 do
    load_kitchen_config('.kitchen.ec2.yml').instances.each do |instance|
      instance.test(:always)
    end
  end

  # Run Each Test Instance in All Test Suites from YAML
  desc 'kitchen - vagrant - test'
  task :vagrant do
    load_kitchen_config('.kitchen.yml').instances.each do |instance|
      instance.test(:always)
    end
  end
end

desc 'Travis CI Tasks'
task travisci: %w(style:chef style:ruby unit:rspec )

desc 'Circle CI Tasks'
task circleci: %w(style:chef style:ruby unit:rspec integration:docker)

desc 'Foodcritic, Rubocop & ChefSpec'
task default: %w(style:chef style:ruby unit:rspec)

desc 'Foodcritic & Rubocop'
task style_only: %w(style:chef style:ruby)
