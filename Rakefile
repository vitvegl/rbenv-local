task :default => 'rbenv:install'

module Rbenv

  INSTALLED_VERSION = ARGV[1]

  def available_versions
    v = `rbenv install -l`
    ver = v.split("\n  "); ver.shift
    return ver
  end
end

namespace :rbenv do
  desc 'Install rbenv into $HOME'
  task :install do
    unless Dir.exists?(File.join(ENV['HOME'], '.rbenv'))
      sh "git clone https://github.com/rbenv/rbenv.git ~/.rbenv"
      sh "git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build"
      File.open(File.join(ENV['HOME'], '.bashrc'), 'a') do |f|
        f.puts("export PATH=\"$PATH:$HOME/.rbenv/bin:$HOME/.rbenv/plugins/ruby-build/bin\"")
        f.puts("eval \"$(rbenv init -)\"")
      end
    else
      puts "~/.rbenv dir exists"
    end
  end

  desc 'Rbenv update'
  task :update => :install do
    sh "cd ~/.rbenv && git pull"
    sh "cd ~/.rbenv/plugins/ruby-build && git pull"
  end
end

namespace :deps do
  desc 'Install builddeps'
  task :install do
    sh "sudo apt-get update && sudo apt-get -y install ruby rake git build-essential libssl-dev libreadline-dev zlib1g-dev"
  end
end

namespace :ruby do
  desc "Install ruby locally into $HOME, using rbenv"
  task :install do
    include Rbenv
    require 'optparse'
    options = {}
    OptionParser.new do |opts|
      opts.banner = "Usage: rake ruby:install -- --version"
      opts.on("--version", String, "RUBY_VERSION") do |version|
        options[:version] = version
      end
    end.parse!
    version = INSTALLED_VERSION
    unless version.nil?
      if available_versions.include?(version)
        Rake::Task['rbenv:update'].invoke
        Rake::Task['deps:install'].invoke
        system "export TMPDIR=\"$HOME/tmp\" && rbenv install #{version}"
      else
        puts "RUBY_VERSION #{version} not available"
      end
    else
      puts "Available versions: #{available_versions}"
    end
    exit
  end
end
