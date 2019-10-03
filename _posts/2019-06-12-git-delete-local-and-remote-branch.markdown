---
layout: post
title:  "Git - Delete local and remove branch"
date:   2019-06-12 12:22:00 +0800
categories: git console
---

To delete a local git branch: 
   
`git branch -d <branch_name>`  
`git branch -D <branch_name>`  

`-d` corresponds to `--delete`, which will only delete the local branch when all local commits/merges have been pushed to remote while `-D` expands to `--delete --force`.  

To delete a remote git branch:  
`git push <remote_name> --delete <branch_name>`


This will then often require on other machines to update the local list of branches
`git remote update <remote_name> --prune`

...in most cases, &gt;remote_name&gt; is `origin`.
