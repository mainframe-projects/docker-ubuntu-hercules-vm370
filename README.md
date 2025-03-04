# A docker container containing a fully running VM/370

Use https://github.com/RattyDAVE/docker-ubuntu-hercules-vm370/issues to send feedback, issues, comments and general chat.

# Part of the retro mini and mainframe series.

* https://hub.docker.com/r/rattydave/alpine-multics - Fully Running Multics mainframe time sharing operating system.
* https://hub.docker.com/r/rattydave/docker-ubuntu-hercules-tss - Fully running version 3.0 of IBM's Time Sharing System/370
* https://hub.docker.com/r/rattydave/docker-ubuntu-hercules-vm370 - vm370 Emulator with Robert O'Hara's Six Pack
* https://hub.docker.com/r/rattydave/docker-ubuntu-hercules-mvs - Fully running MVS 3.8j Tur(n)key 4- System. IBM Mainframe.
* https://hub.docker.com/r/rattydave/alpine-simh/ - AT&T, Data General, DEC PDPs and VAXen, Honeywell, HP and others. All with OSs.
* https://hub.docker.com/r/rattydave/cray1/ - Cray 1 Supercomputer

## Thank you to the following.

* Bob Abele's Original 3-Pack System
* Andy Norrie's 4-Pack system
* Paul Gorinskey's 5-Pack System
* Robert O'Hara's Six Pack *** Now with V1.2 ***

http://www.smrcc.org.uk/members/g4ugm/index.htm  - G4UGM's Vintage and Classic Computer pages

## Usage

```
docker run -dit --name vm370 \
           -p 3270:3270 -p 8038:8038 \
           rattydave/docker-ubuntu-hercules-vm370:latest
```

Connect a 3270 terminal to port 3270 on the docker host.
To get the http://docker.host:8038 for the Hercules console.

Or

```
docker run -dit --name vm370 rattydave/docker-ubuntu-hercules-vm370:latest

docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' vm370
```

Then connect a 3270 terminal to the container ip address on port 3270.

```
x3270 ipaddress:3270
```

To access a console connection - this will also be the console connection on failure.

```
telnet ipaddress 3270
```

To get to the hercules screen in the docker container.

```
docker exec -it vm370 screen -d -RR herc
```


# BETA VM/370 Sixpack Beta 1.3

To use the VM/370 Sixpack Beta 1.3.

```
docker run -dit --name vm370 \
           -p 3270:3270 -p 8038:8038 \
           rattydave/docker-ubuntu-hercules-vm370:beta
```

# Special Usage

You are able to use you own config and disk with this docker container by using the following.


```
docker run -dit --name herc-special \
           -p 3270:3270 -p 8038:8038 \
           -v %LOCAL_DIR%:/opt/hercules/config \
           rattydave/docker-ubuntu-hercules-vm370:latest
```

Example

If all the configuration and disks are located in /root/mainframe the use the following.

```
docker run -dit --name herc-special \
           -p 3270:3270 -p 8038:8038 \
           -v /root/mainframe:/opt/hercules/config \
           rattydave/docker-ubuntu-hercules-vm370:latest
```
      
The container will need a start script ```start.sh``` in the config directory and configured to config file. change %CONFIG_FILE% to your config file.

```
#!/bin/bash
cd /opt/hercules/config
/usr/bin/screen -dm -S herc hercules -f %CONFIG_FILE%
echo /bin/sh
```

Also in the config file I would suggest adding the following line.

```
HTTPPORT       8038
```

This will enable the web interface.

# Users

By default the OPERATOR (password of OPERATOR) userid logs in using the Hercules window.  From the command line you can issue Hercules commands.  To issue commands to the OPERATOR userid you must prefix the command with a single "/".  To shut everything down, first log off any users you have been using.  Then from the Hercules window type "/shutdown" to bring down VM/370.  Finally, issue the Hercules "quit" command.

## There are several userids defined:

* CMSUSER (password of CMSUSER):  This userid is best for general work on the system.  It has several minidisks defined, all residing on a dedicated disk pack.  You can backup any work you store on these disks by simply making a copy of the disk pack's shadow file.
* I recommend you login as CMSUSER (password of CMSUSER) for most general work.  This user's minidisks reside on their own volume (the 6th pack), making backup of your own work much easier.
* MAINT (password of CPCMS):  This is the system programmer's userid.  Logon here if you want to perform system programming tasks.  For example, you could edit the SIXPACK DIRECT A1 file to change the name of CMSUSER to your name, save the file, then issue the DIRECT SIXPACK command to update the sytem directory.  SYSPROG MEMO on MAINT's 191 disk has useful information on how to rebuild the CMS and CP nucleii.
* GCCCMS (password of GCCCMS):  Here you will find the source for the GCC compiler, plus useful EXECs set up to help you do bulk compilations of C programs.
*BREXX (password of BREXX):  Here you will find the source code for the BREXX REXX interpreter.

Type HELP CMSCMDS for an overview of the available commands.

# What is VM/370 and VM/380?

System/370 is a 24-bit architecture:  16MB (2**24) is the maximum addressible memory.  System/380 is the enhanced version of the S/370 architecture created by Paul Edwards, and supported by the 380 version of the Hercules emulator, available at http://mvs380.sourceforge.net.  In the SixPack you can use the S/370-XA instructions BASR and BASM to enter 31-bit mode and address storage above the 16MB line.  Note that this storage is unmanaged, so only one program per Hercules "computer" can use it at a time.  You can access this memory from assembler language programs or from C programs.

# Acknowledgements taken from the readme.txt

The SixPack is brought to you by Robert O'Hara, Paul Edwards, and Dave Wade.  It is based on earlier versions of VM/370 for Hercules developed by Andy Norrie and Paul Gorlinsky.  Thanks to Roc, Martin Taylor, Mike Stramba, and Kevin Leonard for fixes included in version 1.2.

Robert O'Hara, Redmond Washington, October 2010.
