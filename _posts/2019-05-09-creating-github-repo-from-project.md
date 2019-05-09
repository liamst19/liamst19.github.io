---
layout: post
title: Quick notes on how to create a GitHub repository for an existing project
date: 2019-05-09
categories: GitHub
---

A rough workflow for creating a repository for an existing project:

1. Create a new repository in GitHub, e.g., `example-proj`
2. Do `git init` in the project directory
3. If necessary, configure: 
```
$ git config user.name "user-Name"
$ git config user.email "username@example.com"
$ git config credential.helper example-cred
```
4. `git remote add origin https://github.com/user-Name/example-proj.git`
5. `$ git pull origin master`
6. Create/edit `.gitignore`
    7. Add project files: `$ git add .`
8. Commit changes: `$ git commit -m "added project"`
    9. Push changes: `$ git push origin master`

The important point is to do `git pull` before adding and commiting anything, *and then* do `add`, `commit`, and `push`. If you have added and commited before pulling from the remote project, pushing will throw an error. You will have to integrate by pulling with `--allow-unrelated-histories`:

```
$ git pull origin master --allow-unrelated-histories
$ git push --set-upstream origin master
```

