---
layout: post
title: "Octopress: configuration files"
date: 2011-12-02 07:44
comments: true
categories: octopress
---

# _config.yml


<pre>
# ----------------------- #
#      Main Configs       #
# ----------------------- #

url: http://geekontheway.github.com
title: Geekontheway 
subtitle: Stay hungry Stay foolish
author: Bill Zh
simple_search: http://google.com/search

# Default date format is "ordinal" (resulting in "July 22nd 2007")
# You can customize the format as defined in
# http://www.ruby-doc.org/core-1.9.2/Time.html#method-i-strftime
# Additionally, %o will give you the ordinal representation of the day
date_format: "ordinal"

# RSS / Email (optional) subscription links (change if using something like Feedburner)
subscribe_rss: /atom.xml
subscribe_email:
# RSS feeds can list your email address if you like
email:

# ----------------------- #
#    Jekyll & Plugins     #
# ----------------------- #

# If publishing to a subdirectory as in http://site.com/project set 'root: /project'
root: /
permalink: /blog/:year/:month/:day/:title/
source: source
destination: public
plugins: plugins
code_dir: downloads/code
category_dir: blog/categories
markdown: rdiscount
pygments: true # default python pygments have been replaced by pygments.rb

paginate: 10          # Posts per page on the blog index
pagination_dir: blog  # Directory base for pagination URLs eg. /blog/page/2/
recent_posts: 5       # Posts in the sidebar Recent Posts section
excerpt_link: "Read on &rarr;"  # "Continue reading" link text at the bottom of excerpted articles

titlecase: true       # Converts page and post titles to tilecase

# list each of the sidebar modules you want to include, in the order you want them to appear.
# To add custom asides, create files in /source/_includes/custom/asides/ and add them to the list like 'custom/asides/custom_aside_name.html'
default_asides: [asides/recent_posts.html, asides/github.html, asides/twitter.html, asides/delicious.html, asides/pinboard.html, asides/googleplus.html]

# Each layout uses the default asides, but they can have their own asides instead. Simply uncomment the lines below
# and add an array with the asides you want to use.
# blog_index_asides:
# post_asides:
# page_asides:

# ----------------------- #
#   3rd Party Settings    #
# ----------------------- #

# Github repositories
github_user: geekontheway
github_repo_count: 0
github_show_profile_link: true
github_skip_forks: true

# Twitter
twitter_user:
twitter_tweet_count: 4
twitter_show_replies: false
twitter_follow_button: true
twitter_show_follower_count: false
twitter_tweet_button: true

# Google +1
google_plus_one: true
google_plus_one_size: medium

# Google Plus Profile
# Hidden: No visible button, just add author information to search results
googleplus_user: xiaoliang@networking.io
googleplus_hidden: false

# Pinboard
pinboard_user:
pinboard_count: 3

# Delicious
delicious_user:
delicious_count: 3

# Disqus Comments
disqus_short_name:
disqus_show_comment_count: false

# Google Analytics
google_analytics_tracking_id:

# Facebook Like
facebook_like: false
</pre>


# config.rb


<pre>
require 'yaml'
YAML::ENGINE.yamler = 'syck'
project_type = :stand_alone

# Publishing paths
http_path = "/"
http_images_path = "/images"
http_fonts_path = "/fonts"
css_dir = "public/stylesheets"

# Local development paths
sass_dir = "sass"
images_dir = "source/images"
fonts_dir = "source/fonts"

line_comments = false
output_style = :compressed
</pre>


#config.ru

<pre>
require 'bundler/setup'
require 'sinatra/base'

# The project root directory
$root = ::File.dirname(__FILE__)

	class SinatraStaticServer < Sinatra::Base  

	  get(/.+/) do
	      send_sinatra_file(request.path) {404}
		    end

			  not_found do
			      send_sinatra_file('404.html') {"Sorry, I cannot find #{request.path}"}
				    end

					  def send_sinatra_file(path, &missing_file_block)
	    file_path = File.join(File.dirname(__FILE__), 'public',  path)
		    file_path = File.join(file_path, 'index.html') unless file_path =~ /\.[a-z]+$/i  
			    File.exist?(file_path) ? send_file(file_path) : missing_file_block.call
				  end

				  end
				 </pre>


#Rakefile


				  <pre>
				  require "rubygems"
				  require "bundler/setup"
				  require "stringex"

## -- Rsync Deploy config -- ##
# Be sure your public key is listed in your server's ~/.ssh/authorized_keys file
				  ssh_user       = "user@domain.com"
				  ssh_port       = "22"
				  document_root  = "~/website.com/"
				  deploy_default = "push"

# This will be configured for you when you run config_deploy
				  deploy_branch  = "master"

## -- Misc Configs -- ##

				  public_dir      = "public"    # compiled site directory
				  source_dir      = "source"    # source file directory
				  blog_index_dir  = 'source'    # directory for your blog's index page (if you put your index in source/blog/index.html, set this to 'source/blog')
				  deploy_dir      = "_deploy"   # deploy directory (for Github pages deployment)
				  stash_dir       = "_stash"    # directory to stash posts for speedy generation
				  posts_dir       = "_posts"    # directory for blog files
				  themes_dir      = ".themes"   # directory for blog files
				  new_post_ext    = "markdown"  # default new post file extension when using the new_post task
				  new_page_ext    = "markdown"  # default new page file extension when using the new_page task
				  server_port     = "4000"      # port for preview server eg. localhost:4000


				  desc "Initial setup for Octopress: copies the default theme into the path of Jekyll's generator. Rake install defaults to rake install[classic] to install a different theme run rake install[some_theme_name]"
				  task :install, :theme do |t, args|
				    if File.directory?(source_dir) || File.directory?("sass")
					    abort("rake aborted!") if ask("A theme is already installed, proceeding will overwrite existing files. Are you sure?", ['y', 'n']) == 'n'
						  end
						    # copy theme into working Jekyll directories
						    theme = args.theme || 'classic'
							  puts "## Copying "+theme+" theme into ./#{source_dir} and ./sass"
							    mkdir_p source_dir
								  cp_r "#{themes_dir}/#{theme}/source/.", source_dir
								    mkdir_p "sass"
									  cp_r "#{themes_dir}/#{theme}/sass/.", "sass"
									    mkdir_p "#{source_dir}/#{posts_dir}"
										  mkdir_p public_dir
										  end

#######################
# Working with Jekyll #
#######################

										  desc "Generate jekyll site"
										  task :generate do
										    raise "### You haven't set anything up yet. First run `rake install` to set up an Octopress theme." unless File.directory?(source_dir)
											  puts "## Generating Site with Jekyll"
											    system "compass compile --css-dir #{source_dir}/stylesheets"
												  system "jekyll"
												  end

												  desc "Watch the site and regenerate when it changes"
												  task :watch do
												    raise "### You haven't set anything up yet. First run `rake install` to set up an Octopress theme." unless File.directory?(source_dir)
													  puts "Starting to watch source with Jekyll and Compass."
													    system "compass compile --css-dir #{source_dir}/stylesheets" unless File.exist?("#{source_dir}/stylesheets/screen.css")
														  jekyllPid = Process.spawn("jekyll --auto")
														    compassPid = Process.spawn("compass watch")

															  trap("INT") {
																      [jekyllPid, compassPid].each { |pid| Process.kill(9, pid) rescue Errno::ESRCH }
																	      exit 0
																			    }

  [jekyllPid, compassPid].each { |pid| Process.wait(pid) }
  end

  desc "preview the site in a web browser"
  task :preview do
    raise "### You haven't set anything up yet. First run `rake install` to set up an Octopress theme." unless File.directory?(source_dir)
	  puts "Starting to watch source with Jekyll and Compass. Starting Rack on port #{server_port}"
	    system "compass compile --css-dir #{source_dir}/stylesheets" unless File.exist?("#{source_dir}/stylesheets/screen.css")
		  jekyllPid = Process.spawn("jekyll --auto")
		    compassPid = Process.spawn("compass watch")
			  rackupPid = Process.spawn("rackup --port #{server_port}")

			    trap("INT") {
					    [jekyllPid, compassPid, rackupPid].each { |pid| Process.kill(9, pid) rescue Errno::ESRCH }
						    exit 0
								  }

  [jekyllPid, compassPid, rackupPid].each { |pid| Process.wait(pid) }
  end

# usage rake new_post[my-new-post] or rake new_post['my new post'] or rake new_post (defaults to "new-post")
  desc "Begin a new post in #{source_dir}/#{posts_dir}"
  task :new_post, :title do |t, args|
    raise "### You haven't set anything up yet. First run `rake install` to set up an Octopress theme." unless File.directory?(source_dir)
	  mkdir_p "#{source_dir}/#{posts_dir}"
	    args.with_defaults(:title => 'new-post')
		  title = args.title
		    filename = "#{source_dir}/#{posts_dir}/#{Time.now.strftime('%Y-%m-%d')}-#{title.to_url}.#{new_post_ext}"
			  if File.exist?(filename)
	    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
		  end
		    puts "Creating new post: #{filename}"
			  open(filename, 'w') do |post|
			      post.puts "---"
				      post.puts "layout: post"
					      post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
						      post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
							      post.puts "comments: true"
								      post.puts "categories: "
									      post.puts "---"
										    end
											end

# usage rake new_page[my-new-page] or rake new_page[my-new-page.html] or rake new_page (defaults to "new-page.markdown")
											desc "Create a new page in #{source_dir}/(filename)/index.#{new_page_ext}"
											task :new_page, :filename do |t, args|
											  raise "### You haven't set anything up yet. First run `rake install` to set up an Octopress theme." unless File.directory?(source_dir)
											    args.with_defaults(:filename => 'new-page')
												  page_dir = source_dir
												    if args.filename =~ /(^.+\/)?([\w_-]+)(\.)?(.+)?/
													    page_dir += $4 ? "/#{$1}" : "/#{$1}#{$2}/"
														    name = $4 ? $2 : "index"
															    extension = $4 || "#{new_page_ext}"
																    filename = "#{name}.#{extension}"
																	    mkdir_p page_dir
																		    file = page_dir + filename
																			    if File.exist?(file)
	      abort("rake aborted!") if ask("#{file} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
		      end
			      puts "Creating new page: #{file}"
				      open(file, 'w') do |page|
					        page.puts "---"
							      page.puts "layout: page"
								        page.puts "title: \"#{$2.gsub(/[-_]/, ' ')}\""
										      page.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
											        page.puts "comments: true"
													      page.puts "sharing: true"
														        page.puts "footer: true"
																      page.puts "---"
																	      end
																		    else
																			    puts "Syntax error: #{args.filename} contains unsupported characters"
																				  end
																				  end

# usage rake isolate[my-post]
																				  desc "Move all other posts than the one currently being worked on to a temporary stash location (stash) so regenerating the site happens much quicker."
																				  task :isolate, :filename do |t, args|
																				    stash_dir = "#{source_dir}/#{stash_dir}"
																					  FileUtils.mkdir(stash_dir) unless File.exist?(stash_dir)
	  Dir.glob("#{source_dir}/#{posts_dir}/*.*") do |post|
	      FileUtils.mv post, stash_dir unless post.include?(args.filename)
	  end
	  end

	  desc "Move all stashed posts back into the posts directory, ready for site generation."
	  task :integrate do
	    FileUtils.mv Dir.glob("#{source_dir}/#{stash_dir}/*.*"), "#{source_dir}/#{posts_dir}/"
		end

		desc "Clean out caches: .pygments-cache, .gist-cache, .sass-cache"
		task :clean do
		  rm_rf [".pygments-cache/**", ".gist-cache/**", ".sass-cache/**", "source/stylesheets/screen.css"]
		  end

		  desc "Move sass to sass.old, install sass theme updates, replace sass/custom with sass.old/custom"
		  task :update_style, :theme do |t, args|
		    theme = args.theme || 'classic'
			  if File.directory?("sass.old")
			      puts "removed existing sass.old directory"
				      rm_r "sass.old", :secure=>true
					    end
						  mv "sass", "sass.old"
						    puts "## Moved styles into sass.old/"
							  cp_r "#{themes_dir}/"+theme+"/sass/", "sass"
							    cp_r "sass.old/custom/.", "sass/custom"
								  puts "## Updated Sass ##"
								  end

								  desc "Move source to source.old, install source theme updates, replace source/_includes/navigation.html with source.old's navigation"
								  task :update_source, :theme do |t, args|
								    theme = args.theme || 'classic'
									  if File.directory?("#{source_dir}.old")
									      puts "## Removed existing #{source_dir}.old directory"
										      rm_r "#{source_dir}.old", :secure=>true
											    end
												  mkdir "#{source_dir}.old"
												    cp_r "#{source_dir}/.", "#{source_dir}.old"
													  puts "## Copied #{source_dir} into #{source_dir}.old/"
													    cp_r "#{themes_dir}/"+theme+"/source/.", source_dir, :remove_destination=>true
														  cp_r "#{source_dir}.old/_includes/custom/.", "#{source_dir}/_includes/custom/", :remove_destination=>true
														    cp "#{source_dir}.old/favicon.png", source_dir
															  mv "#{source_dir}/index.html", "#{blog_index_dir}", :force=>true if blog_index_dir != source_dir
															    cp "#{source_dir}.old/index.html", source_dir if blog_index_dir != source_dir && File.exists?("#{source_dir}.old/index.html")
																  puts "## Updated #{source_dir} ##"
																  end

##############
# Deploying  #
##############

																  desc "Default deploy task"
																  task :deploy do
																    Rake::Task[:copydot].invoke(source_dir, public_dir)
	  Rake::Task["#{deploy_default}"].execute
	  end

	  desc "Generate website and deploy"
	  task :gen_deploy => [:integrate, :generate, :deploy] do
	  end

	  desc "copy dot files for deployment"
	  task :copydot, :source, :dest do |t, args|
	    exclusions = [".", "..", ".DS_Store"]
		  Dir["#{args.source}/**/.*"].each do |file|
		      if !File.directory?(file) && !exclusions.include?(File.basename(file))
	      cp(file, file.gsub(/#{args.source}/, "#{args.dest}"));
		      end
			    end
				end

				desc "Deploy website via rsync"
				task :rsync do
				  puts "## Deploying website via Rsync"
				    ok_failed system("rsync -avze 'ssh -p #{ssh_port}' --delete #{public_dir}/ #{ssh_user}:#{document_root}")
					end

					desc "deploy public directory to github pages"
					multitask :push do
					  puts "## Deploying branch to Github Pages "
					    (Dir["#{deploy_dir}/*"]).each { |f| rm_rf(f) }
						  Rake::Task[:copydot].invoke(public_dir, deploy_dir)
	  puts "\n## copying #{public_dir} to #{deploy_dir}"
	    cp_r "#{public_dir}/.", deploy_dir
		  cd "#{deploy_dir}" do
		      system "git add ."
			      system "git add -u"
				      puts "\n## Commiting: Site updated at #{Time.now.utc}"
					      message = "Site updated at #{Time.now.utc}"
						      system "git commit -m \"#{message}\""
							      puts "\n## Pushing generated #{deploy_dir} website"
								      system "git push origin #{deploy_branch} --force"
									      puts "\n## Github Pages deploy complete"
										    end
											end

											desc "Update configurations to support publishing to root or sub directory"
											task :set_root_dir, :dir do |t, args|
											  puts ">>> !! Please provide a directory, eg. rake config_dir[publishing/subdirectory]" unless args.dir
											    if args.dir
												    if args.dir == "/"
													      dir = ""
														      else
															        dir = "/" + args.dir.sub(/(\/*)(.+)/, "\\2").sub(/\/$/, '');
																								      end
																									      rakefile = IO.read(__FILE__)
																										      rakefile.sub!(/public_dir(\s*)=(\s*)(["'])[\w\-\/]*["']/, "public_dir\\1=\\2\\3public#{dir}\\3")
																											      File.open(__FILE__, 'w') do |f|
																												        f.write rakefile
																														    end
																															    compass_config = IO.read('config.rb')
																																    compass_config.sub!(/http_path(\s*)=(\s*)(["'])[\w\-\/]*["']/, "http_path\\1=\\2\\3#{dir}/\\3")
																																	    compass_config.sub!(/http_images_path(\s*)=(\s*)(["'])[\w\-\/]*["']/, "http_images_path\\1=\\2\\3#{dir}/images\\3")
																																		    compass_config.sub!(/http_fonts_path(\s*)=(\s*)(["'])[\w\-\/]*["']/, "http_fonts_path\\1=\\2\\3#{dir}/fonts\\3")
																																			    compass_config.sub!(/css_dir(\s*)=(\s*)(["'])[\w\-\/]*["']/, "css_dir\\1=\\2\\3public#{dir}/stylesheets\\3")
																																				    File.open('config.rb', 'w') do |f|
																																					      f.write compass_config
																																						      end
																																							      jekyll_config = IO.read('_config.yml')
																																								      jekyll_config.sub!(/^destination:.+$/, "destination: public#{dir}")
																																									      jekyll_config.sub!(/^subscribe_rss:\s*\/.+$/, "subscribe_rss: #{dir}/atom.xml")
																																										      jekyll_config.sub!(/^root:.*$/, "root: /#{dir.sub(/^\//, '')}")
																																											      File.open('_config.yml', 'w') do |f|
																																												        f.write jekyll_config
																																														    end
																																															    rm_rf public_dir
																																																    mkdir_p "#{public_dir}#{dir}"
																																																	    puts "## Site's root directory is now '/#{dir.sub(/^\//, '')}' ##"
																																																		  end
																																																		  end

																																																		  desc "Set up _deploy folder and deploy branch for Github Pages deployment"
task :setup_github_pages do
  repo_url = get_stdin("Enter the read/write url for your repository: ")
    user = repo_url.match(/:([^\/]+)/)[1]
	  branch = (repo_url.match(/\/[\w-]+.github.com/).nil?) ? 'gh-pages' : 'master'
	    project = (branch == 'gh-pages') ? repo_url.match(/\/([^\.]+)/)[1] : ''
		  unless `git remote -v`.match(/origin.+?octopress.git/).nil?
		      # If octopress is still the origin remote (from cloning) rename it to octopress
			      system "git remote rename origin octopress"
				      if branch == 'master'
					        # If this is a user/organization pages repository, add the correct origin remote
							      # and checkout the source branch for committing changes to the blog source.
								        system "git remote add origin #{repo_url}"
										      puts "Added remote #{repo_url} as origin"
											        system "git config branch.master.remote origin"
													      puts "Set origin as default remote"
														        system "git branch -m master source"
																      puts "Master branch renamed to 'source' for committing your blog source files"
																	      else
																		        unless !public_dir.match("#{project}").nil?
																				        system "rake set_root_dir[#{project}]"
																						      end
																							      end
																								    end
																									  url = "http://#{user}.github.com"
																									    url += "/#{project}" unless project == ''
																										  jekyll_config = IO.read('_config.yml')
																										    jekyll_config.sub!(/^url:.*$/, "url: #{url}")
																											  File.open('_config.yml', 'w') do |f|
																											      f.write jekyll_config
																												    end
																													  rm_rf deploy_dir
																													    mkdir deploy_dir
																														  cd "#{deploy_dir}" do
																														      system "git init"
																															      system "echo 'My Octopress Page is coming soon &hellip;' > index.html"
																																      system "git add ."
																																	      system "git commit -m \"Octopress init\""
																																		      system "git branch -m gh-pages" unless branch == 'master'
																																			      system "git remote add origin #{repo_url}"
																																				      rakefile = IO.read(__FILE__)
																																					      rakefile.sub!(/deploy_branch(\s*)=(\s*)(["'])[\w-]*["']/, "deploy_branch\\1=\\2\\3#{branch}\\3")
																																						      rakefile.sub!(/deploy_default(\s*)=(\s*)(["'])[\w-]*["']/, "deploy_default\\1=\\2\\3push\\3")
																	    File.open(__FILE__, 'w') do |f|
																		      f.write rakefile
																			      end
																				    end
																					  puts "\n---\n## Now you can deploy to #{url} with `rake deploy` ##"
																					  end

																					  def ok_failed(condition)
	  if (condition)
	    puts "OK"
		  else
		      puts "FAILED"
			    end
				end

				def get_stdin(message)
	  print message
	    STDIN.gets.chomp
		end

		def ask(message, valid_options)
	  if valid_options
	      answer = get_stdin("#{message} #{valid_options.to_s.gsub(/"/, '').gsub(/, /,'/')} ") while !valid_options.include?(answer)
		    else
			    answer = get_stdin(message)
	  end
	    answer
		end

		desc "list tasks"
		task :list do
		  puts "Tasks: #{(Rake::Task.tasks - [Rake::Task[:list]]).join(', ')}"
		    puts "(type rake -T for more detail)\n\n"
			end
			</pre>
