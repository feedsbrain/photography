#############################################################################
#
# Modified version of jekyllrb Rakefile
# https://github.com/jekyll/jekyll/blob/master/Rakefile
#
#############################################################################

require 'rake'
require 'date'
require 'yaml'

CONFIG = YAML.load(File.read('_config.yml'))
USERNAME = CONFIG["username"]
REPO = CONFIG["repo"]
SOURCE_BRANCH = CONFIG["branch"]
DESTINATION_BRANCH = "gh-pages"
CNAME = CONFIG["CNAME"]
DESTINATION = CONFIG["destination"]

GIT_NAME = CONFIG["git_name"]
GIT_EMAIL = CONFIG["git_email"]

GH_TOKEN = ENV["GH_TOKEN"]

def check_destination
  unless Dir.exist? DESTINATION
    sh "git clone --branch #{DESTINATION_BRANCH} https://#{USERNAME}:#{GH_TOKEN}@github.com/#{USERNAME}/#{REPO}.git #{CONFIG["destination"]}"
  end
end

namespace :site do
  desc "Generate the site"
  task :build do
    check_destination
    sh "bundle exec jekyll build"
    sh "cp ./ads.txt #{DESTINATION}"
  end

  desc "Generate the site and serve locally"
  task :serve do
    check_destination
    sh "bundle exec jekyll serve"
  end

  desc "Generate the site, serve locally and watch for changes"
  task :watch do
    sh "bundle exec jekyll serve --watch"
  end

  desc "Generate the site and push changes to remote origin"
  task :deploy do
    # Make sure destination folder exists as git repo
    check_destination

    # Set git user name and email for commit
    if ENV["GH_TOKEN"]
      sh "git config --global user.name #{GIT_NAME}"
      sh "git config --global user.email  #{GIT_EMAIL}"
      sh "git config --global push.default simple"
    end

    # Generate the site
    sh "bundle install"
    sh "bundle exec jekyll build"

    # Copy static assets
    sh "cp ./ads.txt #{DESTINATION}"

    # Commit and push to github
    sha = `git log`.match(/[a-z0-9]{40}/)[0]
    Dir.chdir(DESTINATION) do
      # check if there is anything to add and commit, and pushes it
      sh "if [ -n '$(git status)' ]; then
            echo '#{CNAME}' > ./CNAME;
            git add --all .;
            git commit -m 'Updating to #{USERNAME}/#{REPO}@#{sha}.';
            git push;
         fi"
      puts "Pushed updated branch #{DESTINATION_BRANCH} to GitHub Pages"
    end
  end
end
