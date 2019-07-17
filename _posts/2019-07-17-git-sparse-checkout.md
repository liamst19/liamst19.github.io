---
layout: post
title: Using sparse-checkout to work on part of a git project
date: 2019-07-17
categories: GitHub
---

While working on redesigning this site, I thought that it would be nice if I could have a repository dedicated only to handling blog posts. Search led me to [git sparse checkout](https://briancoyner.github.io/2013/06/05/git-sparse-checkout.html):

1.  Initialize a git repository 
    
   ``` bash
   $ git init <name>
   ```
   
2.  Configure to enable sparse checkouts:
    
   ``` bash
   $ git config core.sparsecheckout true
   ```
   
3.  Edit `.git/info/sparse-checkout` to include the target file or directory.
    
   ``` bash
   $ echo target/directory/or/file >> .git/info/sparse-checkout
   ```
   
4.  Add remote:
    
   ``` bash
   $ git remote add -f origin https//github.com/my_github/project.git
   ```
   
5.  Fetch the files
    
   ``` bash
   $ git pull origin master
   ```
   

The resulting git repository should contain only the directory or file specified in `.git/info/sparse-checkout` (In the case above, `target/directory/or/file`).

Now if I could make something like an export template for exporting org subtrees as jekyll post file, it would make these blog posts so much easier to make.
