---
tags: [Linux]
title: Keep_your_vimrc_when_using_Sudo
created: '2020-01-30T20:16:15.675Z'
modified: '2020-01-30T20:43:05.711Z'
---

# Keep your vimrc when using Sudo
Created Friday 21 October 2016

I noticed that when I work on Red Hat servers I have my own vimrc when I type:

$ sudo vim /etc/some_conf_file
I like this option to be able to work with my own configuration file on shared servers. I also didn't want to force other administrators to use my own vimrc. 
However, this great little feature was missing on some Debian Servers I worked on. So after a little bit of search I found the answer:

$ sudo -E vim /etc/some_conf_file 
What does it do? From the man page:

-E    The -E (preserve environment) option indicates to the security policy that the user wishes to preserve their existing environment variables.  The
  security policy may return an error if the -E option is specified and the user does not have permission to preserve the environment.
Hence, you should use this option with care, and don't use it hastily as an alias. 
Know another nifty way to start VIM with your own vimrc without linking /root/.vimrc to ~/.vimrc? I would be happy to know about it!

