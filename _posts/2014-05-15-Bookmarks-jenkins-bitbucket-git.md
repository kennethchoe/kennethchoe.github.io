---
layout: post
title: "Bookmarks - Jenkins, Bitbucket Git on Windows"
published: true
---

### Goal

- Run Jenkins on Windows platform.
- Use bitbucket.org Git as source code repository.
- Hook up Jenkins job when somebody commits code to bitbucket.

### Bookmarks

1. [https://confluence.atlassian.com/display/BITBUCKET/Set+up+SSH+for+Git](https://confluence.atlassian.com/display/BITBUCKET/Set+up+SSH+for+Git)
2. [http://opensourcetester.co.uk/2013/06/28/jenkins-windows-ssh/](http://opensourcetester.co.uk/2013/06/28/jenkins-windows-ssh/)

### Gotchas

1. You don't need PuTTY. OpenSSH can do the job done.
2. When generating SSH key, do not put any password.
3. Set HOME system environment variable to the parent folder of .ssh folder.

When #3 is not done, ```ssh -T git@bitbucket.org``` was giving me ```Permission denied (publickey)``` error when I try from regular DOS command, but worked when I try from git bash.

Note that HOME value is used for your interactive user and also Jenkins user.