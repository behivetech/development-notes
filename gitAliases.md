```
[alias]
	co = checkout
	st = status
	hist = log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
	br = branch
	ci = commit
  rb = pull --rebase origin master
  amend = commit -a --amend
  p = !git push origin $(git rev-parse --abbrev-ref HEAD)
  rbb = "!f() { git pull --rebase origin $1; }; f"
	d = diff
	dc = diff --cached
	la = config --get-regexp alias
	lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%C(bold blue)<%an>%Creset' --abbrev-commit
	lga = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%C(bold blue)<%an>%Creset' --abbrev-commit --all
	old = for-each-ref refs/remotes/origin/ --sort=-committerdate --format='%(HEAD) %(color:yellow)%(refname:short)%(color:reset) - %(color:red)%(objectname:short)%(color:reset) - %(authorname) (%(color:green)%(committerdate:relative)%(color:reset))'
```
