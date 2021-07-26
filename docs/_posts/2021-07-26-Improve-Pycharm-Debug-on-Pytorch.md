---
title:  "Improve-Pycharm-Debug-on-Pytorch"
search: true
categories: 
  - Coding
last_modified_at: 2021-07-26T08:06:00-05:00
---
## Prerequisites
+ A domain name (E.g.: [chongkai.site](https://chongkai.site))
+ Linux system (E.g.: Ubuntu 20.04)
+ Github Account

## Create Github Page Repository
1. Create a repository named **username.github.io**, make it empty


## Config Pages Locally
1. install ruby: 

   ```
   sudo apt-get install ruby-full build-essential zlib1g-dev
   ```
2. install gem, bundler

   ```
   echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
   echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
   echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   gem install bundler
   gem update bundler
   ```
3. clone your io repository: 

   ```
   git clone https://github.com/makecent/makecent.github.io.git
   cd makecent.github.io.git
   ```
4. Init bundler & Install github-pages & Build jekyll:
   
   ```
   bundler init
   ```
   Add this to the first line of your ``Gemfile``:
   gem 'github-pages', group: :jekyll_plugins
   ```
   bundler install
   jekyll build
   ```

7. Test (Locally) & Push

   ```
   bundle exec jekyll serve
   ```
   Open http://localhost:4000 to check your site.
   ```
   git add .
   git commit -m 'Init commit'
   git push -u origin gh-pages
   ```
## Add Theme
Some resources:
+ https://jekyllrb.com/resources/
+ https://jekyllrb.com/docs/themes/
+ https://jekyllthemes.io/github-pages-themes3
+ https://github.com/topics/jekyll-theme
I choosed the *Minimal Mistakes* found in [here](https://github.com/mmistakes/minimal-mistakes)
For simplicity, let's use the theme provided by Github:
1. Go to repository settings-pages
2. Choose a theme and apply.

## (Optional) Link to domain name
1. Add a domain name resolution record of type ``CNMAE``, pointing to ``username.github.io``
2. Go to Settings-Pages. Use your domian name as the ``Custom domain``.
