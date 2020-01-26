---
layout: post
---
Simpler than I thought to set up a jekyll static website up and running with github pages:  
1. clone a repo created with the name: `<githubUserName>.github.io`

2. [Install Ruby](https://www.ruby-lang.org/en/documentation/installation/), [Bundler](https://bundler.io/)   

   ```bash
   $ sudo snap install ruby --classic
   $ gem install bundler
   ```

3. Create Jekyll in the repo directory

   ```bash
   $ jekyll new .
   # Creates a Jekyll site in the current directory
   ```

   

4. Configurations: open `Gemfile` which was just created and follow the instructions

   ![img](https://help.github.com/assets/images/help/pages/gemfile-instructions.png)

5. `$ bundle update` if gem 'rouge' can't be found

5. `$ bundle exec jekyll serve` Run the server locally and edit the content before committing and pushing the ropo
6. git add, commit, push

