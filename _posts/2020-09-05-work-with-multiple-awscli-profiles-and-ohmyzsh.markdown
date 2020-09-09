---
layout: post
title:  "work with multiple awscli profiles and oh-my-zsh"
date:   2020-09-05 09:40:00 +0200
categories: shell zsh cli awscli
---

## Background
If you work with a couple of different AWS accounts and you use a lot the
awscli in oh-my-zsh, it is convenient to know all the time which profile you have currently active. Let's do some plain and simple commando prompt modification...

## there you go...
Oh-my-zsh is a git repository in the home directory (`~/.oh-my-zsh`). I use currently the roby russel theme. It is defined by a theme file (`~/.oh-my-zsh/themes/robbyrussell.zsh-theme`)which also configures the prompt.
Below follows the whole content of robbyrussel.zsh-theme to show the ENV variable AWS_PROFILE in the prompt.

```
PROMPT="%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ )"
PROMPT+=' %{$fg[cyan]%}%c%{$reset_color%}'
PROMPT+='%{$fg[yellow]%} (aws: $AWS_PROFILE)%{$reset_color%}' 
PROMPT+=' $(git_prompt_info)'

ZSH_THEME_GIT_PROMPT_PREFIX="%{$fg_bold[blue]%}git:(%{$fg[red]%}"
ZSH_THEME_GIT_PROMPT_SUFFIX="%{$reset_color%} "
ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[blue]%}) %{$fg[yellow]%}✗"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[blue]%})"
```

The actual addition compared to the orginal is only the third line.

In practice, the AWS cli profile names are stored in the .aws/config file. 
Activation of a profile is then done by exporting the ENV variale `AWS_PROFILE`, eg.:
```
export AWS_PROFILE=my_profile_name
```


