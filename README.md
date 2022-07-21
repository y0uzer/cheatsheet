# Pentester's Cheat Sheet
Quick reference guide to all things pentest



## Basic Linux Commands
> List directory, show hidden, and permissions files:
```$_ ls -la```

> Need to find SUID bit? 
`find / -perm /4000`


## SearchSploit
> After locating the exploit, copy it into the current directory:
`$_ searchsploit -m [exploit_file_path]`
