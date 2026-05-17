Linux is a Operating System
It is open-source
It has different flavours/distribution like Ubuntu, CentOS, RHEL etc

Linux Architechture:
ASK
Architechture/Utility - Terminal
Shell - CLI used to communicating with kernal
Kernel - heart of linux - contains code(c language)
Hardware 

When we write shell command on terminal like for ex mkdir, it goes to kernal which converts it into binary 0/1 and then executes it after checking with hardware

How it works:
power on -> starts -> motherboard(BIOS: Basic Input Output System) -> starts software -> Bootloader -> starts -> Kernal starts process -> first process is PID1 systemd(system daemon) -> starts-> docker,k8s,ssh etc

systemd(system daemon) - background process

Different shell types - bash, sh, zsh

shell is situated in /usr/bin/bash

kernel code is in /(root dir)

Everything in linux is a process 

/bin has all the linux commands

all the files in / has specific uses like /var/log will help check logs

~ is home directory

We can access Linux using:
Docker desktop
WSL
AWS CLI
Virtual Box
VMware
Vagrant
HashiCorp
Git Bash etc

in file system:
d -> directory
l -> link
- -> file
- 
Different process states:
Running: Actively using the CPU right now
Sleeping: Waiting for I/O, a timer, or a signal
Stopped: Paused - usually via Ctrl+z or SIGSTOP
Zombie: Finished but parent hasn't read exit code yet
Uninterruptible: Deep kernel sleep - can't be killed

List 5 commands you would use daily:
cat
ls
systemctl
pwd
cd
