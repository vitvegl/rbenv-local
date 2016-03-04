namespace :rbenv do
  desc 'Install rbenv into $HOME'
  task :install do
    unless Dir.exists?(File.join(ENV['HOME'], '.rbenv'))
      sh "git clone https://github.com/rbenv/rbenv.git ~/.rbenv && \
          git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build && \
          echo 'export PATH='$HOME/.rbenv/bin:$HOME/.rbenv/plugins/ruby-build/bin:$PATH'' >> '~/.bashrc' && \
          echo 'eval '$(rbenv init -)'' >> '~/.bashrc' && source ~/.bashrc"
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
    sh "sudo apt-get update && sudo apt-get -y install ruby rake git libssl-dev libreadline-dev zlib1g-dev"
  end
end
