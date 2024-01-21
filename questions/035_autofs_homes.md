# Containers

### Question
Configure autofs to automatically mount individual users' home directories from `/export/home` on 192.168.55.47 to `/mnt/autofs_home/<user_name>`.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)#

1. Assuming 192.168.55.47 already exports */export/home*...
2. Ensure Autofs is installed
```
dnf install nfs-utils
systemctl enable --now autofs
```
1. Add (indirect) master file `echo "/mnt/autofs_home/ /etc/homes.mapping" >> /etc/auto.master.d/homes.autofs`
2. Create map file `echo "* 192.168.55.47:/export/home/&" >> /etc/homes.mapping`
3. Restart Autofs `systemctl restart autofs`
4. Ensure SELinux allows NFS homes `sudo semanage boolean -m --on use_nfs_home_dirs`