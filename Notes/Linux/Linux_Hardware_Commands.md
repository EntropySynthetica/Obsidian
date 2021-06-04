---
tags: [Linux]
title: Linux_Hardware_Commands
created: '2020-01-30T20:16:15.771Z'
modified: '2020-01-30T20:43:42.888Z'
---

# Linux Hardware Commands
Created Tuesday 12 December 2017


1. lscpu

The lscpu command reports information about the cpu and processing units. It does not have any further options or functionality.


2. lshw 

- List Hardware
A general purpose utility, that reports detailed and brief information about multiple different hardware units such as cpu, memory, disk, usb controllers, network adapters etc. Lshw extracts the information from different /proc files.
<http://www.binarytides.com/linux-lshw-command/>


3. hwinfo 

- Hardware Information
Hwinfo is another general purpose hardware probing utility that can report detailed and brief information about multiple different hardware components, and more than what lshw can report.
<http://www.binarytides.com/linux-hwinfo-command/>


4. lspci 

- List PCI
The lspci command lists out all the pci buses and details about the devices connected to them.
The vga adapter, graphics card, network adapter, usb ports, sata controllers, etc all fall under this category.


5. lsscsi 

- List scsi devices
Lists out the scsi/sata devices like hard drives and optical drives.


6. lsusb 

- List usb buses and device details
This command shows the USB controllers and details about devices connected to them. By default brief information is printed. Use the verbose option "-v" to print detailed information about each usb port


7. Inxi

Inxi is a 10K line mega bash script that fetches hardware details from multiple different sources and commands on the system, and generates a beautiful looking report that non technical users can read easily.


8. lsblk 

- List block devices
List out information all block devices, which are the hard drive partitions and other storage devices like optical drives and flash drives


9. df 

- disk space of file systems
Reports various partitions, their mount points and the used and available space on each.


10. Pydf 

- Python df
An improved df version written in python, that displays colored output that looks better than df


11. fdisk

Fdisk is a utility to modify partitions on hard drives, and can be used to list out the partition information as well.


12. mount

The mount is used to mount/unmount and view mounted file systems.


13. free 

- Check RAM
Check the amount of used, free and total amount of RAM on system with the free command.


14. dmidecode

The dmidecode command is different from all other commands. It extracts hardware information by reading data from the SMBOIS data structures (also called DMI tables).


15. /proc files

Many of the virtual files in the /proc directory contain information about hardware and configurations. 


16. hdparm

The hdparm command gets information about sata devices like hard disks.


