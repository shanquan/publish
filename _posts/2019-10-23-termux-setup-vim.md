---
layout: pagelt
title: Termux 配置 Vim (vundle markdown)
tags: [experience]
---

```bash
pkg install vim
#提供python支持
pkg install vim-python
# vim插件管理，安装vundle
pkg install git
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
vim --version
# 修改系统配置文件
export $VIM=$PREFIX/share/vim
vim $VIM/vimrc
# 修改个人配置文件
vim ~/.vimrc
```
添加以下内容：
```
set nohlsearch
set enc=utf8
set number
syntax on
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'VundleVim/Vundle.vim'
Plugin 'godlygeek/tabular'
Plugin 'plasticboy/vim-markdonw'

call vundle#end()
```
Launch vim, run :PluginInstall

## Vim安装vundle和vim-markdown
```bash
#window 无法识别~路径，需要将.vim/vundle.git移动至~路径下
git clone http://github.com/gmarik/vundle.git ~/.vim/vundle.git
#window scoop的vim配置文件：~/_vimrc
vim ~/.vimrc
```
添加以下内容：
```
set rtp+=~/.vim/vundle.git/ 
 call vundle#rc()

 " Bundles:
 Bundle "L9"
 Bundle "FuzzyFinder"
 Bundle "rails.vim"
 Bundle "ack.vim"
 "
```
Launch vim, run :BundleInstall

## vundle安装插件：vim-markdown
```bash
#windows scoop: vim ~/_vimrc
vim ~/.vimrc
```
添加以下内容：
```
autocmd BufWritePost $MYVIMRC source $MYVIMRC

Plugin 'godlygeek/tabular'
Plugin 'plasticboy/vim-markdonw'
```
Launch vim, run :PluginInstall

## vundle安装vimwiki
修改vimrc文件：
```bash
set nocompatible
filetype plugin on
syntax on

Plugin 'vimwiki/vimwiki'
let g:vimwiki_list = [{'path': '~/vimwiki/',
                      \ 'syntax': 'markdown', 'ext': '.md'}]
```
运行vim +PluginInstall +qall
Then launch Vim, run :Helptags and then :help vimwiki to verify it was installed.

TODO：
FolderSync_Lite 连接坚果云webdav

## Termux配合MarkdownX在Android端使用Markdown

> [stackedit][1]虽然支持github自动同步，但是同步提交不能自己控制，关键是首次加载时间太长。PWA应用又不支持选择github的workspace。移动端仍然可以支持横屏快捷书写，但不支持显示本地图片；坚果云支持上传本地图片；

```bash
＃How do I use my storage in Termux?
＃To grant storage permissions in Android go to Settings>Apps>Termux>Permissions and select storage, then run termux-setup-storage in Termux.
＃Termux进入markdownx文件目录下，执行git clone
cd storage/shared/Android/data/com.ryeeeeee.markdownx/files/notes
git clone https://github.com/shanquan/publish
```
MarkdownX在github项目已经长期未维护，而且没有提供源码，在找到Markor项目之后立马放弃

## Termux+Markor在移动端使用Markdown
```bash
cd storage/shared/Documents/markor/
git clone https://github.com/shanquan/publish
cd publish
```

参考：
- [Termux wiki](https://wiki.termux.com/wiki/Main_Page)
- [VIM Adventures](https://vim-adventures.com)
- [Termux高级终端安装使用配置教程](https://www.cnblogs.com/cutesnow/p/11430833.html)
- [Termux配置使用-简书系列](https://www.jianshu.com/u/6e1067d15b27)
- [Markor](https://github.com/gsantner/markor)
- [markor sync](https://github.com/gsantner/markor/wiki)
- [vimwiki](https://github.com/vimwiki/vimwiki)
- [pandoc](https://pandoc.org/)
- [syncthing](https://github.com/syncthing/syncthing)

[1]: https://stackedit.io/app#providerId=githubWorkspace&owner=shanquan&repo=publish&branch=master