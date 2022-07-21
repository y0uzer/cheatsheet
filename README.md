# Pentester's Cheat Sheet
Quick reference guide to all things pentest.



## Basic Linux Commands
List directory, show hidden, and permissions files
>`ls -la`

Change Permissions for a file or directory:
>`chmod ### file | directory`

Another way of changing permissions over a directory is:
> `chown [user_name] [dir]`

Find files with SUID bit set
>`find / -perm /4000`



## SearchSploit
After locating the exploit, copy it into the current directory.
>`searchsploit -m [exploit_file_path]`



## Recon
Let's look for those open ports and export the output to a specified directory using `nmap`.
>`nmap -sV -sC -vv -oN ./scan 10.10.104.36`

