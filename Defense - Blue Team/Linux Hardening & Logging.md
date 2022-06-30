## Hardening best practices

[Ottima guida](https://www.msp360.com/resources/blog/linux-server-hardening-guide/)

#### Log Files

-   **/var/log/messages** - a general log for Linux systems
-   **/var/log/auth.log** - a log file that lists all authentication attempts (Debian-based systems)
-   **/var/log/secure** - a log file that lists all authentication attempts (Red Hat and Fedora-based systems)
-   **/var/log/utmp** - an access log that contains information regarding users that are currently logged into the system
-   **/var/log/wtmp** - an access log that contains information for all users that have logged in and out of the system
-   **/var/log/kern.log** - a log file containing messages from the kernel
-   **/var/log/boot.log** - a log file that contains start-up messages and boot information



Some examples of interesting analisys is:
```
sudo cat /var/log/secure | grep Accepted
cat /var/log/secure | grep Accepted | awk -F ' ' '{print $1,$2,$6,$9,$10,$11,$14}' | tail -n 5 && cat 2022-06-12-logwatch.txt | grep 'Processing Initiated'
```



## SSH Hardening

Ottime guide, vedere anche questione:

[[SELinux]]
[[Centos & SELinux]]
- Bibbia: https://linuxhandbook.com/ssh-hardening-tips/
- OTP: https://www.linode.com/docs/guides/use-one-time-passwords-for-two-factor-authentication-with-ssh-on-ubuntu-16-04-and-debian-8/
- SAFELY AND CORRECTLY add ssh keys to host: https://linuxhandbook.com/add-ssh-public-key-to-server/