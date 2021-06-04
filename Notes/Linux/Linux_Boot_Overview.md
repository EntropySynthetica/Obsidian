---
tags: [Linux]
title: Linux_Boot_Overview
created: '2020-01-30T20:16:15.763Z'
modified: '2020-01-30T20:43:36.375Z'
---

# Linux Boot Overview
Created Monday 18 June 2018

##### A broad overview of how modern Linux systems boot

From <https://utcc.utoronto.ca/~cks/space/blog/linux/LinuxBootOverview>

For [[reasons beyond the scope of this entry
<https://old.reddit.com/r/programming/comments/8rhvfv/how_does_linux_start_up/e0rm0bu/>]],
today I feel like writing down a broad and simplified overview of how
modern Linux systems boot. Due to being a sysadmin who has stubbed his
toe here repeatedly, I'm going to especially focus on points of failure.

# The system loads and starts the basic bootloader somehow, through either
  BIOS MBR booting or UEFI. This can involve many steps on its own
  and any number of things can go wrong, such as [[unsigned UEFI
  bootloaders on a Secure Boot system Fedora27SecureBootMistake]].
  Generally these failures are the most total; the system reports there's
  nothing to boot, or it repeatedly reboots, or the bootloader aborts
  with what is generally a cryptic error message.

  On a UEFI system, the bootloader needs to live in the EFI system
  partition, which is always a FAT32 filesystem. Some people have
  had luck making this a software RAID mirror with [[the right
  superblock format SoftwareRaidSuperblockFormats]]; see the comments
  on this entry Grub2UEFIBigMistake.

# The bootloader loads its configuration file and perhaps additional
  modules from somewhere, usually your _/boot_ but also perhaps
  your UEFI system partition. Failures here can result in [[extremely
  cryptic errors GrubDiskMismatchError]], dropping you into a GRUB
  shell, or ideally a message saying 'can't find your menu file'.
  The configuration file location is usually hardcoded, which is
  sometimes unfortunate if your distribution has picked [[a bad
  spot Grub2UEFIBigMistake]].

  For GRUB, this spot has to be on a filesystem and storage stack
  that GRUB understands, which is not necessarily the same as what
  your Linux kernel understands. Fortunately GRUB understands a lot
  these days, so under normal circumstances you're unlikely to run
  into this.

  (Some GRUB setups have a two stage configuration file, where the
  first stage just finds and loads the second one. This allows you
  more flexibility in where the second stage lives, which can be
  important on UEFI systems.)

# Using your configuration file, the bootloader loads your
  chosen Linux kernel and [[an initial ramdisk
  <https://en.wikipedia.org/wiki/Initial_ramdisk>]] into memory and
  transfers control to the kernel. The kernel and initramfs image
  also need to come from a filesystem that your bootloader understands,
  but with GRUB the configuration file allows you to be very flexible
  about [[how they're found and where they come from
  MigratingBootFSIntoRootFS]] (and it doesn't have to be the same
  place as _grub.cfg_ is, although on a non-UEFI system both are
  usually in _/boot_).

  There are two things that can go wrong here; your _grub.cfg_ can
  have entries for kernels that don't exist any more, or GRUB can
  fail to locate and bring up the filesystem where the kernel(s)
  are stored. The latter can happen if, for example, [[your _grub.cfg_
  has the wrong UUIDs for your filesystems Grub2MoveBootDisk]].
  It's possible to patch this up on the fly so you can boot your
  system.

# The kernel starts up, creates PID 1, and runs _/init_ from the
  initramfs as PID 1. This process and things that it run then flail
  around doing various things, with the fundamental goal of finding
  and mounting your real root filesystem and transferring control
  to it. In the process of doing this it will try to [[assemble
  software RAID devices and other storage stuff like LVM
  ModernSoftwareRaidAssembly]], perhaps [[set sysctls
  FedoraInitramfsSysctl]], and so on. The obvious and traditional
  failure mode here is that the initramfs can't find or mount your
  root filesystem for some reason; this usually winds up dropping
  you into some sort of very minimal rescue shell. If this happens
  to you, you may want to boot from a USB live image instead; they
  tend to have more tools and a better environment.

  (Sometimes the reasons for failure are [[obscure and annoying
  RaidGrowthGotcha]].)

  On many traditional systems, the initramfs _/init_ was its own
  separate thing, often a shell script, and was thus independent
  from and different from your system's real init. On systemd based
  systems, the initramfs _/init_ is actually systemd itself and so
  even very early initramfs boot is under systemd's control. In
  general, a modern initramfs is a real (root) filesystem that
  processes in the initramfs will see as _/_, and its contents (both
  configuration files and programs) are usually copied from the
  versions in your root filesystem. You can inspect the whole thing
  with _lsinitrd_ or _lsinitramfs_.

  How the initramfs _/init_ pivots into running your real system's
  init daemon on your real system's root filesystem is beyond the
  scope of this entry. The commands may be simple (systemd just runs
  '_systemctl switch-root_'), but how they work is complicated.

  (That systemd is the initramfs _/init_ is convenient in a way,
  because it means that you don't need to learn an additional
  system to inspect how your initramfs works; instead you can
  just look at the systemd units included in the initramfs and
  follow along in the systemd log.)

# Your real init system starts up to perform basic system setup to
  bring the system to a state that we think of as the normal basic
  way it is; basically, this is everything you usually get if you
  boot into a modern single user mode. This does things like set
  the hostname, mount the root filesystem so it can be written to,
  apply your sysctl settings (from the real root filesystem this
  time), configure enough networking so that you have a loopback
  device and the IPv4 and IPv6 localhost addresses, have udev fiddle
  around with hardware, and especially mount all of your local
  filesystems (which includes activating underlying storage systems
  like software RAID and LVM, if they haven't been activated already
  in the initramfs).

  The traditional thing that fails here is that one or more of your
  local filesystems can't be mounted. This often causes this process
  to abort and drop you into a single user rescue shell environment.

  (On a systemd system the hostname is actually set twice, once in
  the initramfs and then again in this stage.)

# With your local filesystems mounted and other core configuration
  in place, your init system continues on to boot your system the
  rest of the way. This does things like configure your network
  (well, perhaps; these days some systems may defer it until you
  log in), start all of the system's daemons, and eventually enable
  logins on text consoles and perhaps start a graphical login
  environment like GDM or LightDM. At the end of this process, your
  system is fully booted.

  Things that fail here are problems like a daemon not starting or,
  more seriously, the system not finding the network devices it
  expects and so not getting itself on the network at all. Usually
  the end result is that you still wind up with a login prompt
  (either a text console or graphics), it's just that there were
  error messages (which you may not have seen) or some things aren't
  working. Very few modern systems abort the boot and drop into a
  rescue environment for failures during this stage.

  On a systemd system, this transfers control from the initramfs
  systemd to the systemd binary on your root filesystem (which takes
  over as PID 1), but systemd maintains continuity of its state and
  boot process and you can see the whole thing in _journalctl_. The
  point where the switch happens is reported as 'Starting Switch
  Root...' and then 'Switching root.'

All of System V init, Upstart, and systemd have this distinction
between the basic system setup steps and the later 'full booting'
steps, but they implement it in different ways. Systemd doesn't
draw a hard distinction between the two phases and you can shim
your own steps into either portion in basically the same way. System
V init tended to implement the early 'single user' stage as a
separate nominal runlevel, runlevel 'S', that the system transitioned
through on the way to its real target runlevel. Upstart is sort of
a hybrid; it has [[a _startup_ event
<https://www.systutorials.com/docs/linux/man/7-startup/>]] that's
emitted to trigger a number of things before things start fully
booting.

(This really is an overview. Booting Linux on PC hardware has become
a complicated process at the best of times, with a lot of things to
set up and fiddle around with.)

