### 执行 brew cleanup 命令后，再用brew指令执行其他命令如：brew --help 会报以下错误
```
Traceback (most recent call last):
	3: from /usr/local/Homebrew/Library/Homebrew/brew.rb:23:in `<main>'
	2: from /usr/local/Homebrew/Library/Homebrew/brew.rb:23:in `require_relative'
	1: from /usr/local/Homebrew/Library/Homebrew/global.rb:12:in `<top (required)>'
/usr/local/Homebrew/Library/Homebrew/global.rb:12:in `require': cannot load such file -- active_support/core_ext/object/blank (LoadError)
```
解决方案：
需要重新设置下brew的配置 

执行命令：brew update-reset