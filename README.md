# Pentester's Cheat Sheet
Quick reference guide to all things pentest.



## Recon
### Nmap
Let's look for those open ports and export the output to a specified directory using `nmap`.
```
nmap -sV -sC -vv -oN ./scan [ip_address]
```

### SMBMap
SMBMap allows users to enumerate samba share drives across an entire domain. List share drives, drive permissions, share contents, upload/download functionality, file name auto-download pattern matching, and even execute remote commands. This tool was designed with pen testing in mind, and is intended to simplify searching for potentially sensitive data across large networks.
```
smbmap -H [ip_address]
```
If an error is returned, try the `-u` switch and declare a username.
```
smbmap -H [ip_address] -u test
```



## Enumeration
### Linpeas
LinPEAS is a script that search for possible paths to escalate privileges on Linux/Unix*/MacOS hosts.
```
# 1. Copy over the LinPEAS script over OpenSSH using ~scp~ 
$ scp [LinPEAS_file_path] [user]@[ip_address]:[file_path] <-- Psst! /dev/shm is a great place

# 2. Change directory to ensure file transfer was successful and set file as executable 
$ chmod +x [file_name].sh

# 3. Now its time to let LinPEAS work its magic and find some escalation vectors. Run the following:
$ ./linpeas.sh | tee linlog.txt

For more information, check out LinPEAS Github: https://github.com/carlospolop/PEASS-ng/
```



## Cracking
### Hydra
Hydra is a parallelized login cracker which supports numerous protocols to attack. It is very fast and flexible, and new modules are easy to add.
#### SSH
```
hydra -l [username] -P [wordlist] [ip_address] ssh
```

John the Ripper is a tool designed to help systems administrators to find weak (easy to guess or crack through brute force) passwords, and even automatically mail users warning them about it, if it is desired.

Run private SSH key against ssh2john to produce a new hash file. Next, crack the hash using specified wordlist. This should provid 
a password for password protected private keys. 
#### ssh2john
```
ssh2john [key_id_rsa] > forjohn.txt
john forjohn.txt --wordlist=[path to wordlist]
```



## Exploits
Exploit Database Archive Search After locating the exploit, copy it into the current directory.
```
searchsploit -m [exploit_file_path]
```



## Basic Linux Commands
List directory, show hidden, and permissions files
```
ls -la
```

Change Permissions for a file or directory:
```
chmod ### [file] or [directory]
```

Another way of changing permissions over a directory is:
```
chown [user_name] [dir]
```

Find files with SUID bit set:
```
find / -perm /4000
```
>Or (if using is in sudoers file).
```
sudo -l
```



## Random Tips & Tricks
### RSA Keys
```
# RSA Keys only like to be read by authenticated users. Be sure to change permissions to:
$ chmod 600 [RSA_Key_file_name]
```
