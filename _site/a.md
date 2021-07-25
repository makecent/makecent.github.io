## Prerequisites
+ A domain name (E.g.: [chongkai.site](https://chongkai.site))
+ Linux system (E.g.: Ubuntu 20.04)
+ Github Account

## Create Github Page Repository
1. Create a repository named **makecent.github.io**
2. Add a domain name resolution record of type ``CNMAE``, pointing to ``makecent.github.io``
3. Go to Settings-Pages. Use your domian name as the ``Custom domain``

## Config Pages
1. install ruby, gem: 

   ```
   sudo apt-get install ruby-full build-essential zlib1g-dev
   ```
2. install bundle, jekyll bundler

   ```
   echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
   echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
   echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   gem install jekyll bundler
   ```
3. clone your io repository: 

   ```
   git clone https://github.com/makecent/makecent.github.io.git
   ```
4. Init jekyll
"215" is the version of ``github-pages`` found here ["Dependency versions."](https://pages.github.com/versions/)
![image](https://user-images.githubusercontent.com/42603768/126895368-98b584db-7d99-4746-bf81-25bc39fbee5c.png)

   gem "github-pages", "~>GITHUB-PAGES-VERSION", group: :jekyll_plugins
```
Bundler could not find compatible versions for gem "terminal-table":
  In snapshot (Gemfile.lock):
    terminal-table (>= 2.0.0)

  In Gemfile:
    github-pages (~> 215) was resolved to 215, which depends on
      terminal-table (~> 1.4)

Running `bundle update` will rebuild your snapshot from scratch, using only
the gems in your Gemfile, which may resolve the conflict.

```
