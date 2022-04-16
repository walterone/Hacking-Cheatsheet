## What to look for
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