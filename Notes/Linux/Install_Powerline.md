---
tags: [Linux]
title: Install_Powerline
created: '2020-01-30T20:16:15.611Z'
modified: '2020-01-30T20:42:39.030Z'
---

# Install Powerline
Created Wednesday 12 October 2016


1. Install pre requisites


 sudo apt-get install vim-nox git python-pip fontconfig


2. Install Powerline


 sudo pip install <git+git://github.com/Lokaltog/powerline>


3. Install the required fonts


 wget <https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf>
 wget <https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf>
 sudo mv PowerlineSymbols.otf /usr/share/fonts/
 sudo fc-cache -vf
 sudo mv 10-powerline-symbols.conf [/etc/fonts/conf.d/](file:///etc/fonts/conf.d)


4. Add the following to your ~/.vimrc


 set rtp+=/usr/local/lib/python2.7/dist-packages/powerline/bindings/vim/

 " Always show statusline
 set laststatus=2

 " Use 256 colours (Use this setting only if your terminal supports 256 colours)
 set t_Co=256


5. Add the following to your ~/.bashrc


 if [ -f /usr/local/lib/python2.7/dist-packages/powerline/bindings/bash/powerline.sh ]; then
 source /usr/local/lib/python2.7/dist-packages/powerline/bindings/bash/powerline.sh
 fi


6. Add the following to your ~/.tmux.conf


 source /usr/local/lib/python2.7/dist-packages/powerline/bindings/tmux/powerline.conf
 set-option -g default-terminal "screen-256color"

