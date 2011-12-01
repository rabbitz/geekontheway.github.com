---
layout: post
title: "Octopress: The first day"
date: 2011-12-01 07:44
comments: true
categories: octopress
---
#Octopress Setup

>First, I want to stress that Octopress is a blogging framework for hackers. You should be comfortable running shell commands and familiar with the basics of Git. If that sounds daunting, Octopress probably isn’t for you.

####Before You Begin
>You’ll need to install Git and set up your Ruby environment. Octopress requires Ruby **1.9.2** wich you can easily install with RVM or rbenv.

If `ruby --version` doesn’t say you’re using Ruby 1.9.2, you may want to revisit your RVM installation.

 >$git clone git://github.com/imathis/octopress.git octopress
  >
   >$cd octopress

   If you use RVM, You'll be asked if you trust the .rvmrc file (say yes).

####Next, install dependencies.
   >$ bundle install

   Mostly,you will see
   <pre>
   Using rake (0.9.2) 
	Installing RedCloth (4.2.8) with native extensions 
	Installing posix-spawn (0.3.6) with native extensions 
	Installing albino (1.3.3) 
	Installing blankslate (2.1.2.4) 
	Installing chunky_png (1.2.1) 
	Installing fast-stemmer (1.0.0) with native extensions 
	Installing classifier (1.3.3) 
	Installing fssm (0.2.7) 
	Installing sass (3.1.5) 
	Installing compass (0.11.5) 
	Installing directory_watcher (1.4.0) 
	Installing ffi (1.0.9) with native extensions 
	Installing haml (3.1.2) 
	Installing kramdown (0.13.3) 
	Installing liquid (2.2.2) 
	Installing syntax (1.0.0) 
	Installing maruku (0.6.0) 
	Installing jekyll (0.11.0) 
	Installing rubypython (0.5.1) 
	Installing pygments.rb (0.1.3) 
	Installing rack (1.3.2) 
	Installing rb-fsevent (0.4.3.1) with native extensions 
	Installing rdiscount (1.6.8) with native extensions 
	Installing rubypants (0.2.0) 
	Installing tilt (1.3.2) 
	Installing sinatra (1.2.6) 
	Installing stringex (1.3.0) 
	Using bundler (1.0.21) 
	Your bundle is complete! Use `bundle show [gemname]` to see where a bundled gem is installed.
	</pre>
	**Notes**:you may get error like:

	<pre>
	rake aborted!
	You have already activated rake 0.9.2.2, but your Gemfile requires rake 0.9.2. Using bundle exec may solve this.

	(See full trace by running task with --trace)
	</pre>

	As it to me,i just solve it by add `alias rake="bundle exec rake` to my **.bashrc**.

####Install the default Octopress theme.


	>$rake install

	----------
#Configuring Octopress

	Here’s a list of files for configuring Octopress.

	- _config.yml       # Main config (Jekyll's settings)
	- Rakefile          # Configs for deployment
	- config.rb         # Compass config
	- config.ru         # Rack config

	Configurations in the Rakefile are mostly related to deployment and you probably won’t have to touch them unless you’re using rsync.

####Blog Configuration

	In the _config.yml there are three sections for configuring your Octopress Blog. Spoiler: You must change url, and you’ll probably change title, subtitle and author and enable some 3rd party services.

	Main Configs

	- url:                # For rewriting urls for RSS, etc
	- title:              # Used in the header and title tags
	- subtitle:           # A description used in the header
	- author:             # Your name, for RSS, Copyright, Metadata
	- simple_search:      # Search engine for simple site search
	- subscribe_rss:      # Url for your blog's feed, defauts to /atom.xml
	- subscribe_email:    # Url to subscribe by email (service required)
	- email:              # Email address for the RSS feed if you want it.

####Jekyll & Plugins

	These configurations are used by Jekyll and Plugins. If you’re not familiar with Jekyll, you should probably have a look at the [configuration docs](https://github.com/mojombo/jekyll/wiki/Configuration) which lists more options that aren’t covered here.

	- root:               # Mapping for relative urls (default: /)
	- permalink:          # Permalink structure for blog posts
	- source:             # Directory for site source files
	- destination:        # Directory for generated site files
	- plugins:            # Directory for Jekyll plugins
	- code_dir:           # Directory for code snippets (for include_code plugin)
	- category_dir:       # Directory for generated blog category pages 
	- pygments:           # Toggle python pygments syntax highlighting
	- paginate:           # Posts per page on the blog index
	- pagination_dir:     # Directory base for pagination URLs eg. /blog/page/2/
	- recent_posts:       # Number of recent posts to appear in the sidebar 
	- default_asides:     # Configure what shows up in the sidebar and in what order
	- blog_index_asides:  # Optional sidebar config for blog index page
	- post_asides:        # Optional sidebar config for post layout
	- page_asides:        # Optional sidebar config for page layout

	If you want to change the way permalinks are written for your blog posts, see Jekyll’s permalink docs.

	Note: Jekyll has a baseurl config which offers mock subdirectory publishing support by adding a redirect to Jekyll’s WEBrick server. Please don’t use this. If you want to publish your site to a subdirectory, (see Deploying Octopress to a Subdirectory).

####3rd Party Settings

	These third party integrations are already set up for you. Simply fill in the configurations and they’ll be added to your site.

	- Twitter Setup a sidebar twitter feed, follow button, and tweet button (for sharing posts and pages).
	- Google Plus One Setup sharing for posts and pages on Google’s plus one network.
	- Pinboard Share your recent Pinboard bookmarks in the sidebar.
	- Delicious Share your recent Delicious bookmarks in the sidebar.
	- Disqus Comments Add your disqus short name to enable disqus comments on your site.
	- Google Analytics Add your tracking id to enable Google Analytics tracking for your site.

	The Octopress layouts read these configurations and only include the javascript and html necessary for the enabled services.

	----------
#Blogging Basics

	Octopress offers some rake tasks to create post and pages preloaded with metadata and according to Jekyll’s naming conventions.

####Blog Posts

	Blog posts must be stored in the source/_posts directory and named according to Jekyll’s naming conventions: YYYY-MM-DD-post-title.markdown. The name of the file will be used as the **url slug**, and the date helps with file distinction and determines the sorting order for post loops.

	Octopress provides a rake task to create new blog posts with the right naming conventions, with sensible yaml metadata.


	> rake new_post["title"]

	new_post expects a naturally written title and strips out undesirable url characters when creating the filename. The default file extension for new posts is markdown but you can configure that in the Rakefile.

	Example


	> rake new_post["Zombie Ninjas Attack: A survivor's retrospective"]

	The filename will determine your url. With the default permalink settings the url would be something like `http://site.com/blog/2011/07/03/zombie-ninjas-attack-a-survivors-retrospective/index.html`

	Open a post in a text editor and you’ll see a block of ** yaml front matter** which tells Jekyll how to processes posts and pages.

	<pre>
	---
	layout: post
	title: "Zombie Ninjas Attack: A survivor's retrospective"
	date: 2011-07-03 5:59
	comments: true
	categories:
	---
	</pre>

	Here you can **turn comments off and or categories** to your post. If you are working on a multi-author blog, you can add **author: Your Name** to the metadata for proper attribution on a post. If you are working on a draft, you can add  **published: false** to prevent it from being posted when you generate your blog.

	You can add a single category or multiple categories like this.



	>categories: Sass

	or

	>categories: [CSS3, Sass, Media Queries]

####New Pages

	You can add pages anywhere in your blog **source directory** and they’ll be parsed by Jekyll. The URL will correspond directly to the filepath, so about.markdown will become site.com/about.html. If you prefer the URL site.com/about/ you’ll want to create the page as about/index.markdown. Octopress has a rake task for creating new pages easily.


	> rake new_page[super-awesome]

	 creates /source/super-awesome/index.markdown


	 > rake new_page[super-awesome/page.html]

	  creates /source/super-awesome/page.html

	  Like with the new post task, the default file extension is markdown but you can configure that in the Rakefile. A freshly generated page might look like this.

	  <pre>
	  ---
	  layout: page
	  title: "Super Awesome"
	  date: 2011-07-03 5:59
	  comments: true
	  sharing: true
	  footer: true
	  ---
	  </pre>

	  The title is derived from the filename so you’ll likely want to change that. This is very similar to the post yaml except it doesn’t include categories, and you can toggle sharing and comments or remove the footer altogether. If you don’t want to show a date on your page, just remove it from the yaml.

####Generate & Preview


	  > rake generate   # Generates posts and pages into the public directory
	  > 
	  > rake watch      # Watches source/ and sass/ for changes and regenerates
	  > 
	  > rake preview    # Watches, and mounts a webserver at http://localhost:4000

	  Using the rake preview server is nice, but If you’re a POW user, you can set up your Octopress site like this.


	  > cd ~/.pow

	  > ln -s /path/to/octopress octopress

	  Now that you’re setup with POW, you’ll just run rake watch and load up http://octopress.dev instead.

	  ----------

#Deploying to Github Pages

###With Github User/Organization pages

	  Use this if you want to host a blog from http://username.github.com (though you can also use custom domains).

	  Create a new Github repository and name the repository with your user name or organization name username.github.com or organization.github.com.

	  Github Pages for users and organizations uses the master branch like the public directory on a web server, serving up the files at your Pages url http://username.github.com. 

	  As a result, you’ll want to **work on the source for your blog in the source branch and commit the generated content to the master branch.** Octopress has a configuration task that helps you set all this up.


	  > $ rake setup_github_pages

	  <pre>
	  Enter the read/write url for your repository: git@github.com:geekontheway/geekontheway.github.com.git
	  Added remote git@github.com:geekontheway/geekontheway.github.com.git as origin
	  Set origin as default remoteMaster branch renamed to 'source' for committing your blog source files
	  Initialized empty Git repository in /home/nasa/www/octopress/_deploy/.git/
	  [master (root-commit) 75592aa] Octopress init
	   1 files changed, 1 insertions(+), 0 deletions(-)
	 create mode 100644 index.html

## Now you can deploy to http://geekontheway.github.com with `rake deploy` ##
	 </pre>

	 This will:

	 - Ask you for your Github Pages repository url.
	 - Rename the remote pointing to imathis/octopress from ‘origin’ to ‘octopress’
	 - Add your Github Pages repository as the default origin remote.
	 - Switch the active branch from master to source.
	 - Configure your blog’s url according to your repository.
	 - Setup a master branch in the _deploy directory for deployment.

	 Next run:

	 > rake generate
	 > 
	 > rake deploy

	 This will generate your blog, copy the generated files into _deploy/, add them to git, commit and push them up to the master branch. In a few seconds you should get an email from Github telling you that your commit has been received and will be published on your site.

	 Don’t forget to commit the source for your blog.

	 > git add .
	 > 
	 > git commit -m 'your message'
	 > 
	 > git push origin source

	 After all done ,your config may looks like:

	 <pre>
	 nasa@ubuntu:~/www/octopress$ git remote -v

	 octopress	git://github.com/imathis/octopress.git (fetch)
	 octopress	git://github.com/imathis/octopress.git (push)
	 origin	git@github.com:geekontheway/geekontheway.github.com.git (fetch)
	origin	git@github.com:geekontheway/geekontheway.github.com.git (push)

	nasa@ubuntu:~/www/octopress$ git branch -a

	* source
	  remotes/octopress/HEAD -> octopress/master
	    remotes/octopress/compass
		  remotes/octopress/configuration
		    remotes/octopress/edge
			  remotes/octopress/generate_environment
			    remotes/octopress/gh-pages
				  remotes/octopress/master
				    remotes/octopress/move_rakefile_configs
					  remotes/octopress/post_names
					    remotes/octopress/rake_minify_js
						  remotes/octopress/refactor_code_highlight
						    remotes/octopress/refactor_deployment
							  remotes/octopress/refactor_js
							    remotes/octopress/site
								  remotes/octopress/site-deploy-test
								    remotes/octopress/subdir
									  remotes/octopress/thor
									    remotes/origin/source

										</pre>

####Custom Domains

										First you’ll need to create a file named CNAME in the source containing your domain name.


										> echo 'your-domain.com' >> source/CNAME

										From Github’s Pages guide:

										Next, you’ll need to visit your domain registrar or DNS host and add a record for your domain name. For a sub-domain like www.example.com you would simply create a CNAME record pointing at charlie.github.com. If you are using a top-level domain like example.com, you must use an A record pointing to 207.97.227.245. Do not use a CNAME record with a top-level domain it can have adverse side effects on other services like email. Many DNS services will let you set a CNAME on a TLD, even though you shouldn’t. Remember that it may take up to a full day for DNS changes to propagate, so be patient.

										----------


