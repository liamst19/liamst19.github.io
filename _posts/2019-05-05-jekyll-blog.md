---
layout: post
title: Setting up Ruby+Devkit/Bundler/Jekyll on Windows, for github.io
date: 2019-05-05
categories: GitHub jekyll
---
I've been playing around with jekyll for this GitHub Pages site. The instructions I came across were a bit confusing, and it took some time to get it reliably set up (although I am still not sure how reliable this site is). As record I'm listing the steps here, that finally seemed to work.

## Installing Jekyll on Windows

Here are the links to the instructions I followed:

- [Using Jekyll with Bundler - Jekyll](https://jekyllrb.com/tutorials/using-jekyll-with-bundler/)
- [Setting up your GitHub Pages site locally with Jekyll - GitHub](https://help.github.com/en/articles/setting-up-your-github-pages-site-locally-with-jekyll)
- [Notes on: Install Jekyll (and Ruby) on Windows for GitHub pages - marckassay](https://gist.github.com/marckassay/45f186d0f51a1d58621a0cf4acb10457)

### 1. Download and install [Ruby+Devkit](https://rubyinstaller.org/).

Jekyll runs on Ruby+Devkit, [downloaded from here](https://rubyinstaller.org/). Rubyinstaller by default also installs "MSYS2 Development Toolchain" inside the installation directory. There was an option to uncheck and skip this step, if you already have it installed elsewhere. One problem I've encountered was that the only directory Rubyinstaller checks for seems to be `C:\msys64`; other names don't seem to be recognized, and I had to rename my `MSYS2` directory. When installer finishes, there is an option to run `ridk`, which attempts to install the toolchain inside MSYS2. You can also open console (`cmd`) and run:

```
$ ridk install 2 3
```

The `2 3` after `install` sets `ridk` to look for and update MSYS2 installation and then install the toolchain. `1` installs MSYS2 into the ruby directory.

Check that ruby was installed and included in `PATH` by entering `ruby -v` into console.

### 2. Install Jekyll and [Bundler](https://bundler.io/).

```
$ gem install jekyll bundler
```

Check that Jekyll installed properly with `jekyll -v`.

### 3. Initialize Bundler

With the preliminary installation finished, now begins the steps to set up a Jekyll project using Bundler. The instructions on GitHub and Jekyll sites felt to me a bit confusing. It appears that now we're using Bundler to generate Jekyll files and directories *into the project directory*.

Initialize a git project: `$ git init <project name>` and move to the project directory (`$ cd <project name`). Now, in console, run:

```
$ bundle init

$ bundle install --path vendor/bundle
```

`init` generates a file called `Gemfiles`, and `install --path vendor/bundle` creates the `./vendor/bundle/` subdirectory, where Bundler will be installing Ruby gem files.

### 4. Install Jekyll as dependency

```
$ bundle add jekyll
```

### 5. Create a Jekyll scaffold

```
$ bundle exec jekyll new --force --skip-bundle .

$ bundle install
```

Note the `.` at the end, which specifies the current directory as path. Now there is a working jekyll website, which can be served. To test, run:

```
$ bundle exec jekyll serve
```

A skeleton website with a "minima" theme should now be accessible by visiting [http://127.0.0.1:4000](http://127.0.0.1:4000) in your browser.

### 6. Set up Jekyll for GitHub

Open `Gemfiles` with a text editor. Find the following line, around line 11:

```
gem "jekyll", "~> 3.8.5"
```

Apparently, the latest version of jekyll is not compatible with GitHub Pages. Change "`3.8.5`" to "`3.7.3`". There should also be the following comment around line 16:

```
# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
# gem "github-pages", group: :jekyll_plugins
```

Uncomment the third line (remove the `#`) which begins with `# gem "github-pages"`. Now in console execute:

```
$ bundle update 
```

Now, when pushed to GitHub `master` in a `<username>.github.io` repository, the website appear on `http://<username>.github.io/`. If pushed into some other project repository, as a branch named `gh-pages`, the pages will appear in `http://<username>.github.io/<project-name>` (this is not yet verified by me). It would be good to create or edit `.gitignore` to exclude the following from being commited to git:

```
# ignore jekyll build directory
/_site
.sass-cache/
.jekyll-metadata

/vendor

# Ruby
.bundle/
.byebug_history
.ruby-gemset
.ruby-version
*.gem
Gemfile.lock
```

There may be others that should be excluded.

### Side note:

While running `bundle exec jekyll serve`, I noticed that it kept stopping, and after attempting several times to restart the server, `jekyll` disappeared all together, giving the message:

```
'jekyll' is not recognized as an internal or external command
operable program or batch file.
```

`ruby.exe` itself seems to have disappeared too. After looking around, I found that my antivirus software caught `jekyll.bat` and `ruby.exe` as false positives and removed them quietly. I was able to recover them from the virus chest; if this happens, the solution seems to be to ruby and msys directories as exceptions, but I am worried this might not be the most secure way.
