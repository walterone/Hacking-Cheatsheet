# Updating Kali
The following updates should be applied (and can be done safely) on the [32-bit PWK Kali VM](https://support.offensive-security.com/pwk-kali-vm/) periodically, especially before a major event (such as an exam attempt or lab extension).

## Table of Contents
* [searchsploit](#searchsploit)
* [nmap](#nmap)
* [wpscan](#wpscan)
* [locate](#locate)

## searchsploit
```bash
searchsploit -u
```

## nmap
```bash
nmap --script-updatedb
```

## wpscan
```bash
wpscan --update
```

## locate
```bash
updatedb
```