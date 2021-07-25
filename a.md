## Prerequisites
+ A domain name (E.g.: [chongkai.site](https://chongkai.site))
+ Linux system (E.g.: Ubuntu 20.04)
+ Github Account

## Create Github Page Repository
1. Create a repository named **makecent.github.io**
2. Add a domain name resolution record of type ``CNMAE``, pointing to ``makecent.github.io``
3. Go to Settings-Pages. Use your domian name as the ``Custom domain``

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
5. Test (Locally)

   ```
   bundle exec jekyll serve
   ```
   Open http://localhost:4000 to check your site.
