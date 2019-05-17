---
layout: post
title: Email address in GitHub
date: 2019-05-17
categories: GitHub
---

It appears that, if you don't configure your account email settings correctly in GitHub, it's possible that your email address is exposed in your commit history:

 - [GitHub Privacy 101: How to remove personal emails from your public repos - freeCodeCamp](https://medium.freecodecamp.org/github-privacy-101-how-to-remove-personal-emails-from-your-public-repos-58347b06a508)


GitHub does provide an option to mask your commit email address, replacing it with a redirect e-mail which looks like: `<a generated number>+<your username>@users.noreply.github.com`. It's under `Personal Settings` → `Emails`.

There is also a way to go through and edit your commit histories. The following has the instructions with a bash script you can run:

 - [Changing author info -  GitHub Help](https://help.github.com/en/articles/changing-author-info).
 
 This seems tough to do if you have many repositories, but I suppose that someone who has that many would be likely to know how to automate on his or her own.
