# 我的 vim 配置文件






![blog-vim](https://pengshp.coding.net/p/images/d/images/git/raw/master/blog-vim.png "vim")

我常用到的vim配置文件备份，纯vim配置，不包含任何插件，做一个记录备份，方便多系统迁移使用。



<!--more-->

## 安装vim

```
# CentOS
~$ sudo yum install -y vim

# Debian/Ubuntu
~$ sudo apt install -y vim

# Arch Linux
~$ sudo pacman -Sy vim
```

## 配置文件

Debian/Ubuntu 放在`/etc/vim/vimrc.local`下用于全局配置，对所有用户都生效

放在`~/.vimrc`只对当前登录用户生效。

```shell
~$ vim ~/.vimrc
"开启语法高亮
syntax on
" 检测文件类型
filetype on
" 设置在Vim中可以使用鼠标，防止终端无法拷贝
if has('mouse')
   set mouse-=a
endif
" 显示当前行号和列号
set ruler
" 在状态栏显示正在输入的命令
set showcmd
" 左下角显示当前Vim模式
set showmode
" 括号匹配模式
set showmatch
" 显示行号
set number

" 设置tab宽度
set tabstop=4

" 设置自动对齐空格数
set shiftwidth=4

"设置（软）制表符宽度为4
set softtabstop=4

" 用space替代tab的输入 
set expandtab

"不用space替代tab的输入
" set noexpandtab

" 智能自动缩进
set smartindent

" 自动对齐
set autoindent

" 设置编码方式
set encoding=utf-8

set helplang=cn

set magic
set cursorline

" 搜索相关
set hlsearch
set incsearch

"不要备份文件
set nobackup
```

### 参考链接

1.[vim教程网](https://vimjc.com/)

2.[vim官网](https://www.vim.org/)
