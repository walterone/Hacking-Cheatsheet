## General
We can reach our objective by downloading a VHD dump or even by mounting a remote share contianing a VHD backup.

In order to mount shares see the respective [[SMB]] or [[NFS]] pages.



## Requirements

```bash
sudo apt install libguestfs-tools

#similar to rhen mounting shares, a new folder has to be created.
guestmount --add ~/pentests/HTB/Bastion/mount/WindowsImageBackup/L4mpje-PC/Backup\ 2019-02-22\ 124351/9b9cfbc3-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro ~/pentests/HTB/Bastion/vhd -v

```

After this command the tool will do a boot-up sequence in order to expand and mount the virtual HD.


