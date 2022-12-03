| [README](README.md) | [subject](subject_ru.md) | [How to start a web server](howTo.md) | defense |
|-|-|-|-|

# Defense the project

# LEVEL 00

From subject: "Then, you will be able to connect using the following couple of login:password: level00:level00."

In terminal:

```
ssh level00@192.168.56.5 -p 4242
>> yes
>> password: level00
```

level00@192.168.56.5's password: level00
level00@SnowCrash:~$
level00@SnowCrash:~$ ls -la
	total 12
	dr-xr-x---+ 1 level00 level00  100 Mar  5  2016 .
	d--x--x--x  1 root    users    340 Aug 30  2015 ..
	-r-xr-x---+ 1 level00 level00  220 Apr  3  2012 .bash_logout
	-r-xr-x---+ 1 level00 level00 3518 Aug 30  2015 .bashrc	
	-r-xr-x---+ 1 level00 level00  675 Apr  3  2012 .profile

level00@SnowCrash:~$ cat .profile 
	# ~/.profile: executed by the command interpreter for login shells.
	# This file is not read by bash(1), if ~/.bash_profile or ~/.bash_login
	# exists.
	# see /usr/share/doc/bash/examples/startup-files for examples.
	# the files are located in the bash-doc package.
	# the default umask is set in /etc/profile; for setting the umask
	# for ssh logins, install and configure the libpam-umask package.
	#umask 022
	# if running bash
	if [ -n "$BASH_VERSION" ]; then
    	# include .bashrc if it exists
    	if [ -f "$HOME/.bashrc" ]; then
		. "$HOME/.bashrc"
    	fi
	fi
	# set PATH so it includes user's private bin if it exists
	if [ -d "$HOME/bin" ] ; then
    	PATH="$HOME/bin:$PATH"
	fi
	level00@SnowCrash:~$ 

# LEVEL 01

# LEVEL 02

# LEVEL 03

# LEVEL 04

# LEVEL 05

# LEVEL 06

# LEVEL 07

# LEVEL 08

# LEVEL 09

# LEVEL 10

# LEVEL 11

# LEVEL 12

# LEVEL 13

# LEVEL 14


| [README](README.md) | [subject](subject_ru.md) | [How to start a web server](howTo.md) | defense |
|-|-|-|-|
