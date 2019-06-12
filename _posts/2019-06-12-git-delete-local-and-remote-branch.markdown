---
layout: post
title:  "Git - Delete local and remove branch"
date:   2019-06-12 12:22:00 +0800
categories: git console
---

To delete a local git branch: 
   
`git <branch_name> -d <feature_branch_name>`  
`git <branch_name> -D <feature-branch_name>`  

`-d` corresponds to `--delete`, which will only delete the local branch when all local commits/merges have been pushed to remote while `-D` expands to `--delete --force`.  

To delete a remote git branch:  
`git push <remote_name> --delete <branch_name>`