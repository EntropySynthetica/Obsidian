---
tags: [Linux]
title: tmux_Cheat_Sheet
created: '2020-01-30T20:16:15.951Z'
modified: '2020-01-30T20:54:16.762Z'
---

# tmux Cheat Sheet
Created Tuesday 16 January 2018


*****

start new:
tmux
OR
tmux new

start new in 256 color mode (useful for colorful bash and vim layouts)
tmux -2

start new with session name:
tmux new -s myname

attach:
tmux a  #  (or at, or attach)

attach to named:
tmux a -t myname

list sessions:
tmux ls

kill session:
tmux kill-session -t myname

Kill all the tmux sessions:
tmux ls | grep : | cut -d. -f1 | awk '{print substr($1, 0, length($1)-1)}' | xargs kill


Session Control:

In tmux, hit the prefix ctrl+b then the following session control command key,

— Misc
d  detach
t  big clock
?  list shortcuts
:  prompt

— Sessions
:new<CR>  new session
s  list sessions
$  name session

— Windows (Tabs)
c  create window
w  list windows
n  next window
p  previous window
f  find window
,  name window
&  kill window

— Panes (splits)
%  vertical split
"  horizontal split

o  swap panes
q  show pane numbers
x  kill pane
+  break pane into window (e.g. to select text by mouse to copy)
-  restore pane from window
⍽  space - toggle between layouts
<prefix> q (Show pane numbers, when the numbers show up type the key to goto that pane)
<prefix> { (Move the current pane left)
<prefix> } (Move the current pane right)
<prefix> z toggle pane zoom
[   Enter copy mode to move around the page.  Use this for scrolling. 

![](./tmux_Cheat_Sheet/tmux-3-1.png)

