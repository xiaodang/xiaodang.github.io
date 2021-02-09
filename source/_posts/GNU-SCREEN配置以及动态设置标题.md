---
title: GNU SCREEN配置以及动态设置标题
date: 2021-02-09 16:07:49
categories: 编程
tags:
---
最近开始使用GNU的screen来管理terminal的窗口，但是当开窗口多的时候，会面临不知道每个窗口是做什么的，默认的窗口标题也非常不友好，官网文档也没太多描述，最后好不容易找到了设置方法，记录一下配置。

.zshrc文件里添加如下配置
```
# dynamic titles for screen
preexec () {
  echo -ne "\ek${(s: :)1}\e\\"
```

.screenrc文件里添加如下配置
```
startup_message off

# GNU Screen - main configuration file
# All other .screenrc files will source this file to inherit settings.
# Author: Christian Wills - cwills.sys@gmail.com

# Allow bold colors - necessary for some reason
attrcolor b ".I"

# Tell screen how to set colors. AB = background, AF=foreground
termcapinfo xterm 'Co#256:AB=\E[48;5;%dm:AF=\E[38;5;%dm'
# Enables use of shift-PgUp and shift-PgDn
# termcapinfo xterm|xterms|xs|rxvt ti@:te@
termcapinfo xterm* ti@:te@
# Erase background with current bg color
defbce "on"

# Enable 256 color term
term xterm-256color

# Cache 30000 lines for scroll back
defscrollback 30000

# change command character from ctrl-a to ctrl-b (emacs users may want this)
#escape ^Bb


### pass commands to screen for describing windows
shelltitle '$ |zsh'

### set caption
caption always '%{= kw}[ %{y}%H%{-} ][ %= %-Lw%{+b M}%n%f* %t%{-}%+LW %= ][ %{r}%l%{-} ][ %{c}%c%{-} ]'
```