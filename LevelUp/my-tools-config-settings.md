# Tooling

## AI Arsenal

- General
	- [ChatGPT](https://chatgpt.com/)
	- [Claude](https://claude.ai/)
	- [DeepSeek](https://www.deepseek.com/)
	- [Grok](https://x.com/i/grok)
	- [Gemini](https://gemini.google.com/)
- Web Searching
	- [Perplexity](https://www.perplexity.ai/)
	- [You.com](https://you.com/)
- Coding
	- [Github Copilot](https://github.com/features/copilot)
- Notes/Revision
	- [NotebookLM](https://notebooklm.google.com/)
- Domain Specific
	- Data Charts/Diagrams
		- [Napkin.ai](https://www.napkin.ai/)

Ref. https://www.synthesia.io/post/ai-tools

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
- [Backup and Restore Brew Packages](https://gist.github.com/JoeyBurzynski/cbab8361c59a720d60f83c20e8b21e20)
- [10 CLI tools that made the biggest impact](https://itnext.io/10-cli-tools-that-made-the-biggest-impact-f8a2f4168434)
