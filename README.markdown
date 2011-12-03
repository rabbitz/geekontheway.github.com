#Get started

##For forkers:

- `git clone git@github.com:yourname/geekontheway.github.com.git`

`cd` to it.
                                                                                                                                
- `git checkout source`
- `git branch -D master`
- `bundle install`
- `rake setup_github_pages`

###after your changes:

- `rake generate`
- `rake deploy`
- `git add .`
- `git commit -m 'msg'`
- `git push origin source`

Then send me a pull request.