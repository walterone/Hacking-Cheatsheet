# Port Scanning


## Nmap General Rules
#### When pivoting do not overload with heavy scans!

```bash
sudo nmap -O -sT -Pn -v -T4 -oA eztcp <ip>
```


#### Finding nmap's scripts

```bash
locate .nse | grep ftp

nmap --script-help ftp-anon
```
	
#### Generate comma-separated list of scripts to run, to be used as ```--script``` argument
```bash
#Untested
for s in `ls -l /usr/share/nmap/scripts/* | cut -d"/" -f 6 | cut -d"." -f 1`; do echo -ne "$s,"; done
```
Fine-tune results by modifying "ls" command.

***
## Nmap TCP
   
####  Quick
```bash
sudo nmap -sV -sC -O -T5 -n -Pn -vv -oA fasttcp <IP>
```

####  Full TCP
```bash
sudo nmap -sV -sC -O -T5 -n -Pn -p- -vv -oA fulltcp <IP>
```

####  Full TCP + Full Scripts
```bash
sudo nmap -sV -sC --script "vuln and safe" -O -p- -n -Pn -vv -oA fullscan <IP>
```

#### Vuln Check
```bash
sudo nmap -T4 -n -sC -sV -p- -oN nmap-versions --script='*vuln*' [ip]
```

#### Fuck you scan.
```bash
sudo nmap -vv -p <portn> --scripts all <ip>
```

***
## Nmap Vulnerability Scan

```bash
#Scan a target using all NSE vuln scripts
nmap -p 80 --script=*vuln* $ip

#Scan a target using all HTTP vulns NSE scripts.
nmap -p 80 --script=http*vuln* $ip

#Scan entire network for FTP servers that allow anonymous access.
nmap -p 21 --script=ftp-anon $ip/24

#Scan entire network for a directory traversal vulnerability. It can even retrieve admin's password hash.
nmap -p 80 --script=http-vuln-cve2010-2861 $ip/24

#Scan a smb target.
nmap -p 445,135 --script=smb*vuln* $ip
```

***
## Nmap UDP

####  Top 100   
```bash
sudo nmap -sU -sV --version-intensity 0 -n -F -T4 <IP>
```

#### Top 1000 
```bash
sudo nmap -sU -sV --version-intensity 0 -n -T4 <IP>
```

####  Top 100 and launch scripts
```bash
sudo nmap -sU -sV -sC -n -F <IP>
```

*** 
## UDP Proto Scanner

[Github](https://github.com/CiscoCXSecurity/udp-proto-scanner)

```bash
./udp-proto-scanner.pl <IP>  
```





