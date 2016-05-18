task :default => 'rbenv:install'

module Rbenv

  INSTALLED_VERSION = ARGV[1]

  def installed_versions
    v = `rbenv versions`.split("\n")
    v.map do |i|
      i.strip!
    end
    v
  end

  def available_versions
    v = `rbenv install -l`.split("\n  ")
    v.shift
    v
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
    sh "sudo apt-get update && sudo apt-get -y install ruby rake git build-essential \
libssl-dev libreadline-dev zlib1g-dev libgmp-dev libdbm-deep-perl libgdbm3 libgdbm-dev \
libtcl8.5 tcl8.5 tcl8.5-dev tk8.5 tk8.5-dev doxygen valgrind libffi-dev"
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
        if !installed_versions.include?(version)
          Rake::Task['rbenv:update'].invoke
          Rake::Task['deps:install'].invoke
          opts = "--with-tcl-include=/usr/include/tcl8.5/ --with-tcllib=tcl8.5 --with-tklib=tk8.5"
          tmpdir = File.join(ENV['HOME'], '/tmp')
          if version =~ /^\d/ or version =~ /^ree/
            system "export TMPDIR=#{tmpdir} && RUBY_CONFIGURE_OPTS=\"#{opts}\" rbenv install #{version}"
          else
            system "export TMPDIR=#{tmpdir} && rbenv install #{version}"
          end
        else
          puts "RUBY_VERSION #{version} installed"
        end
      else
        puts "RUBY_VERSION #{version} not available"
      end
    else
      puts "Available versions: #{available_versions}"
    end
    exit
  end
end
