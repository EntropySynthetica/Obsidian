---
tags: [Linux]
title: Hard_Reboot_Via_Keystroke
created: '2020-01-30T20:16:15.599Z'
modified: '2020-01-30T20:42:25.677Z'
---

# Hard Reboot Via Keystroke
Created Wednesday 14 June 2017

Taken from the bottom of this article, <http://www.binarytides.com/linux-command-shutdown-reboot-restart-system/>


5. REISUB - R E I S U B key strokes


The above shown commands can be used when you are in control of your system. What if the system has hanged and is not responding at all. And you do not want to press the power button on the CPU which might lead to data corruption. To save from such a situation, comes the magic sysRQ keys.

A special combination of key presses that will allow you to reboot your linux system, no matter how much it is hanged. Check the wikipedia article. for more information.

A common use of the magic SysRq key is to perform a safe reboot of a Linux computer which has otherwise locked up. This can prevent a fsck being required on reboot and gives some programs a chance to save emergency backups of unsaved work.
Warning : Pressing the following keys would instantly reboot your system. Its similar to pressing the power button of your CPU or executing the reboot -f command.

ALT + PrintScreen + B
Now in place of the B key we have to use R E I S U letters first. Each key does a task as mentioned below

unRaw      (take control of keyboard back from X),
 tErminate (send SIGTERM to all processes, allowing them to terminate gracefully),
 kIll      (send SIGKILL to all processes, forcing them to terminate immediately),
  Sync     (flush data to disk),
  Unmount  (remount all filesystems read-only),
reBoot.

1. Hold down the Alt and SysRq (Print Screen) keys.
2. While holding those down, type the following keys in order, several seconds apart: R E I S U B
3. Computer should reboot.


Make sure to have some time gap between each of keys R E I S U B.

The sysrq feature can be controlled by changing the value of /proc/sys/kernel/sysrq. To check if sysrq is enabled on the system or not, echo the value. It should be non zero.

$ cat /proc/sys/kernel/sysrq
176
####

