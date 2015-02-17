require 'rubygems'
require 'rake'
require 'fileutils'
require 'yaml'

 #== Configuration =============================================================

# Set "rake watch" as default task
task :default => :watch

# Load the configuration file
CONFIG = YAML.load_file("_config.yml")

# Get and parse the date
DATE = Time.now.strftime("%Y-%m-%d")

# Directories
POSTS = "_posts"
DRAFTS = "_drafts"


desc "Draft a new post"
task :new do
  puts "What should we call this post for now?"
  name = STDIN.gets.chomp
  FileUtils.touch("drafts/#{name}.md")

  open("drafts/#{name}.md", 'a') do |f|
    f.puts "---"
    f.puts "layout: post"
    f.puts "title: \"DRAFT: #{name}\""
    f.puts "---"
  end
end


desc "Startup Jekyll"
task :start do
  sh "jekyll --server"
end

### :push task taken from https://github.com/imathis/octopress/blob/master/Rakefile ###

## config for push task. 
deploy_branch   = "master"
deploy_dir      = "/Users/richarddev/Projects/richardplacide.github.io"
public_dir      = "_site"
remote_name     = "origin"


desc "copy dot files for deployment"
task :copydot, :source, :dest do |t, args|
  FileList["#{args.source}/**/.*"].exclude("**/.", "**/..", "**/.DS_Store", "**/._*").each do |file|
    cp_r file, file.gsub(/#{args.source}/, "#{args.dest}") unless File.directory?(file)
  end
end


desc "deploy public directory to github pages"
multitask :push do
  puts "## Deploying branch to Github Pages "
  puts "## Building site"
  system "jekyll build"
  puts "\n## jekyll site built"
  puts "## Pulling any updates from Github Pages "
  cd "#{deploy_dir}" do 
    system "git pull"
  end
  (Dir["#{deploy_dir}/*"]).each { |f| rm_rf(f) }
  Rake::Task[:copydot].invoke(public_dir, deploy_dir)
  puts "\n## Copying #{public_dir} to #{deploy_dir}"
  cp_r "#{public_dir}/.", deploy_dir
  cd "#{deploy_dir}" do
    system "git add -A"
    puts "\n## Commiting: Site updated at #{Time.now.utc}"
    message = "Site updated at #{Time.now.utc}"
    system "git commit -m \"#{message}\""
    puts "\n## Pushing generated #{deploy_dir} website"
    system "git push #{remote_name} #{deploy_branch}"
    puts "\n## Github Pages deploy complete"
  end
end

task :default => :start

