# Tooling

## Git

Config settings
- [Medium Article](https://medium.com/geekculture/a-step-by-step-guide-to-setting-up-git-config-global-properties-db6dbce30fa8)
- Working across Crossplatform git clients (MacOS, Linux v/s Windows) : Set `git config --global core.autocrlf false`

```ini
[filter "lfs"]
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f
	process = git-lfs filter-process
	required = true
[push]
	autoSetupRemote = true
[alias]
	fpush = push
	st = status
	hist = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
[branch]
	sort = -committerdate
[column]
	ui = auto
[commit]
	gpgSign = true
[core]
	autocrlf = false
```

## Terminal

Essential tools and modifications for Terminal
- [7 Must have CLI tools](https://www.youtube.com/watch?v=mmqDYw9C30I)
- 
