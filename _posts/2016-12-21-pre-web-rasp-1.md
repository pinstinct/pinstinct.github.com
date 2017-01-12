---
layout: post
title: Pre-SCHOOL 1 X Raspberry Pi
categories: wps
tags: [fastcampus, vim, python, code]
---

## 1. OS 설치

[MINIBIAN](https://minibianpi.wordpress.com/) based on Debian

## 2. 필요 SW 설치

```sh
$ apt-get update
$ apt-get install raspi-config
$ apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev git wget curl llvm libncurses5-dev libncursesw5-dev xz-utils
```

## 3. Shell 설정

### 1. zsh 설치

```sh
$ apt-get install zsh
$ zsh --version

# 기본 shell을 bash에서 zsh로 변경
$ chsh -s `which zsh`
$ echo $SHELL
```

### 2. Oh My Zsh 설치 (for zsh 환경관리)

```sh
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

#### Custom directory Setting

```sh
$ mkdir zsh_custom
$ cd zsh_custom
$ mkdir themes
$ mkdir plugins
```

```vim
" ~/.zshrc file
ZSH_CUSTOM=$HOME/zsh_custom
```

#### Theme

```sh
$ cd $ZSH_CUSTOM/themes/

# External themes Downlaod
$ curl -O http://raw.github.com/caiogondim/bullet-train-oh-my-zsh-theme/master/bullet-train.zsh-theme
```

```vim
" ~/.zshrc file
# ZSH_THEME="agnoster"
ZSH_THEME="bullet-train"
```

#### Plug in

```sh
# zsh-autosuggestions plugin Downlaod
$ git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

# zsh-syntax-highlighting plugin Downlaod
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

$ source ~/.zshrc
```
```vim
" ~/.zshrc file
plugins=(zsh-autosuggestions zsh-syntax-highlighting)
```




## 4. vim 설정

### 1. vim 설치

```sh
$ apt-get install vim
```



### 2. pathogen.vim 설치(for vim 환경관리)

```sh
$ mkdir -p ~/.vim/autoload ~/.vim/bundle && \
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
$ execute pathogen#infect()
```

```vim
" ~/.vimrc file
execute pathogen#infect()
syntax on
filetype plugin indent on
```

#### Theme

```sh
$ cd ~/.vim/bundle
$ git clone git://github.com/altercation/vim-colors-solarized.git
```

```vim
" ~/.vimrc file
set background=dark
colorscheme solarized
let g:solarized_termcolors=256

" line number
set nu

" tab to space4
set tabstop=4
set shiftwidth=4
set expandtab
set smartindent
```

## 5. pyenv 설치

```sh
$ git clone https://github.com/yyuu/pyenv.git ~/.pyenv
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshenv
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshenv
$ echo 'eval "$(pyenv init -)"' >> ~/.zshenv
$ exec $SHELL
$ pyenv install 3.5.2
```

#### 에러 발생 1

```sh
locale.Error: unsupported locale setting
Makefile:1060: recipe for target 'install' failed

$ export LANGUAGE=en_US.UTF=8
$ export LC_ALL=C
$ locale

$ pyenv install 3.5.2
Installed Python-3.5.2
```

#### 설치 확인

``` sh
$ pyenv shell 3.5.2
$ python --verion
Python 3.5.2
```
