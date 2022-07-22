# Pentester's Cheat Sheet
Quick reference guide to all things pentest.



## Basic Linux Commands
List directory, show hidden, and permissions files
>`ls -la`

Change Permissions for a file or directory:
>`chmod ### [file] or [directory]`

Another way of changing permissions over a directory is:
> `chown [user_name] [dir]`

Find files with SUID bit set:
>`find / -perm /4000`
Or (if using is in sudoers file).
>`sudo -l`


## Cracking
### Hydra
Hydra is a parallelized login cracker which supports numerous protocols to attack. It is very fast and flexible, and new modules are easy to add.
#### SSH
>`hydra -l [username] -P [wordlist] IP ssh`

John the Ripper is a tool designed to help systems administrators to find weak (easy to guess or crack through brute force) passwords, and even automatically mail users warning them about it, if it is desired.

Run private SSH key against ssh2john to produce a new hash file. Next, crack the hash using specified wordlist. 
### ssh2john
```
ssh2john [key_id_rsa] > forjohn.txt
john forjohn.txt --wordlist=[path to wordlist]
```

## Exploits
Exploit Database Archive Search After locating the exploit, copy it into the current directory.
>`searchsploit -m [exploit_file_path]`



## Recon
### Nmap
Let's look for those open ports and export the output to a specified directory using `nmap`.
>`nmap -sV -sC -vv -oN ./scan 10.10.104.36`

### SMBMap
SMBMap allows users to enumerate samba share drives across an entire domain. List share drives, drive permissions, share contents, upload/download functionality, file name auto-download pattern matching, and even execute remote commands. This tool was designed with pen testing in mind, and is intended to simplify searching for potentially sensitive data across large networks.
>`smbmap -H 10.10.207.34`
If an error is returned, try the `-u` switch and declare a username.
>`smbmap -H 10.10.207.34 -u test`


## Enumeration
### Linpeas
LinPEAS is a script that search for possible paths to escalate privileges on Linux/Unix*/MacOS hosts. The checks are explained on book.hacktricks.xyz