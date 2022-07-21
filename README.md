# Pentester's Cheat Sheet
Quick reference guide to all things pentest.



## Basic Linux Commands
List directory, show hidden, and permissions files:
>`ls -la`

Find files with SUID bit set
>`find / -perm /4000`


## SearchSploit
After locating the exploit, copy it into the current directory:
>`searchsploit -m [exploit_file_path]`
