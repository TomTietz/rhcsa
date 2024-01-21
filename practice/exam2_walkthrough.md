# Exam Walkthrough

Another fictional exam. This exam is structured differently than the other walkthrough and the questions. Look through the commands listed here and try understadn their effect.

## Node1

### Network settings
```bash
# Check existing config
hostnamectl
ip a s
nmcli con show
nmcli con show eno1

# Edit relevant values
nmcli con modify eth0 ipv4.adresses 10.10.34.221/24 ipv4.gateway 10.10.34.1 ipv4.dns 10.10.34.1
nmcli con up eth0
hostnamectl hostname new_hostname
            
# Check results
systemctl reboot
hostnamectl
ip a s
nmcli con show
nmcli con show eno1
```


### Add repos
```bash
# Check existing repos
dnf repolist all

# If necessary (in case you forget the syntax) copy existing repo file
cp /etc/yum.repos.d/fedora.repo /etc/yum.repos.d/local.repo
# Edit repo file
vim /etc/yum.repos.d/local.repo
> [AppStream]
> name=AppStream
> baseurl=http://repo.blablablablabla/AppStream
> enabled=1
> gpgcheck=0
> 
> [BaseOS]
> name=BaseOS
> baseurl=http://repo.blablablablabla/BaseOS
> enabled=1
> gpgcheck=0

# Cleanup
dnf clean all

# Check results
dnf repolist all
```


### Debug SELinux
```bash
# Ensure httpd and SELinux are installed
dnf install -y httpd
dnf install -y policycoreutils*

# Start and enable web server and firewall
systemctl enable --now httpd
systemctl enable --now firewalld

# Allow httpd to access /var/www/html/
ls -lZ /var/www/html/
semanage fcontext -a -t httpd_sys_content_t "/var/www/html/(/.*)?"
restorecon -R -v /webq

# Enable port 82 for HTTP
seemanage port -l | grep http_port_t
semanage port -a -t http_port_t -p tcp 82

# Create firewall rule
firewall-cmd --permanent --add-port=82/tcp
firewall-cmd --reload

# Edit httpd config
vim /etc/httpd/conf/httpd.conf
> <virtualhost YOUR_IP:82>
> servername YOUR_HOSTNAME
> documentroot /var/www/html
> </virtualhost>
# Search for and edit this line
> Listen 82
# Heck for correct syntax
httpd -t
# Restart httpd
systemctl restart httpd

# Check results
systemctl status httpd
seemanage port -l | grep http
ls -lRZ /var/www/html/
firewall-cmd --list-all
curl http://localhost:82/file1
```


### Create users
```bash
# List existing users
getent passwd
getent group

# Create group
groupadd sysmgrs
useradd -G symgrs natasha
useradd -G symgrs harry
useradd -s /usr/sbin/nologin sarah

# Set their passwords
passwd harry
passwd natasha
passwd sarah

# Check results
getent passwd
getent group
id harry; id sarah; id natasha
```


### Folder permsisions
```bash
# Check (if dir exists)
ls -ld /management

# Create dir
mkdir -p /management

# Change group
chgrp symgrs /management

# Change permissions
chmod 770 /management

# Set gid
chmod g+s

# Check results
ls -ld /management
su - harry
touch /management/test.txt
ls -l /management
```


### Use autofs to mount remote home directoriess
```bash
# Ensure NFS is installed
dnf install -y nfs-utils autofs

# Browse remote files
mkdir /mountpoint
mount -t nfs server:/ /mountpoint
ls -R /mountpoint
umount /mountpoint

# Create mount directory
mkdir -p /remotehomes

# Create master map file (indirect)
echo "/remotehomes /etc/auto.homes" >> /etc/auto.master.d/homes.autofs

# Create mapping file (indirect) (CHOOSE ONE)
echo "remoteuser16 -rw,sync server:/remotehomes/remoteuser16" >> /etc/auto.homes
echo "* -rw,sync server:/remotehomes/&" >> /etc/auto.homes
echo "remoteuser16 -ro,sync server:/remotehomes/remoteuser16" >> /etc/auto.homes

# Start autofs service
systemctl enable --now autofs 

# Specify SELinux context
semanage fcontext -a -e /home /remotehomes
semanage fcontext -a -t user_home_dir_t /remotehomes/*
restorecon -R -v /remotehomes

semanage boolean -l | grep nfs
semanage boolean --modify --on use_nfs_home_dirs_t

# Check results
df -hT
cd /remotehomes/remoteuser16
ls -ld .
df -hT
su - remoteuser
ls -lR ~
df
```


### Create crontab job for user
```bash
# List existing crontabs
crontab -lu harry

# Edit crontabs
crontab -eu harry
> 30 12 * * 1-5 /bin/echo "hello"

# Check results 
crontab -lu harry

# Deny natasha the ability to crontab
echo "natasha" >> /etc/cron.deny

# Check results
su - natasha
crontab -e
```

### Create system crontab job
```bash
# List existing crontabs
cat /etc/crontab

# Edit crontabs
vim /etc/crontab
> 30 12 * * 1-5 root /bin/echo "hello"

# Check results 
cat /etc/crontab
```


### Configure NTP
```bash
# Ensure chronyd is installed
dnf install -y chrony*

# Edit server in config
vim /etc/chrony.conf
# Write this line right under the specified pool
> server SERVER_IP iburst

# Restart service
systemctl restart chronyd

# CHeck results
chronyc sources
```

### Find & Copy
```bash
# Create dir for copies
mkdir /harriesstuff

# Find and copy
find /etc/ -user harry -type f -exec cp {} /harriesstuff/ \; 

# Check results
ls -l /harriestuff
```


### Create specific user
```bash
# Create user with UID
useradd -u 1234 jeff

# Give him a password
echo "secure" | passwd --stdin jeff

# Check results
tail -1 /etc/passwd
```


### Create tar archive
```bash
# Create archive
tar -cvzf /root/backup.tar.gz /var/local

# Check results
ls -l /root
```


### Default permissions
```bash
# Change user
su - harry

# Check current permissions
umask
touch test
ls -l test
rm test

# Persistently set umask
echo "umask 077" >> /home/harry/.bash_profile

# Load bash profile
source /home/harry/.bash_profile

# Check results
umask
mkdir test_dir
touch test
ls -l
rm test
rmdir test_dir

# Exit user
exit
```


### Password Expiration New User
```bash
# Edit config
vim /etc/login.defs
> PASS_MAX_DAYS 20

# Check results
useradd new
chage -l new
userdel new
```


### Assign sudo privilege
```bash
vim /etc/sudoers
> %sysmgrs ALL=(ALL) NOPASSWD=ALL

# Check results
su - harry
sudo -l
exit
```


### Reset Root Passwd
```bash
# Edit rescue kernel parameters
# Add rd.break to kernel line 
mount -o remount,rw /sysroot
chroot /sysroot
passwd root
touch /.autorelabel
exit
exit

# Check results by login in
cat /etc/redhat-release
```


### Create SWAP
```bash
# See current
lsblk [--fs]

# Start interactive parted with device
parted /dev/sdb

# Set unit
> unit MiB
# Show existing partitions
> print
# Create swap partition
> mkpart primary linux-swap 1MiB 512MiB
# Exit
> quit

## Check results
lsblk --fs

# Create swap space on partition
mkswap /dev/sdb1

# Persitently mount the swap
lsblk --output=UUID /dev/sdb1
echo "UUID=$(lsblk --output=UUID /dev/sdb1 | tail -1)    swap swap defaults 0 0" >> /etc/fstab
swapon -a

# Check results
lsblk
free -h
```


### Create LV
```bash
# See current
lsblk [--fs]

# Start interactive parted with device
parted /dev/sdb

# Set unit
> unit MiB
# Show existing partitions
> print
# Create swap partition
> mkpart primary 513MiB 1024MiB
# Exit
> quit

# Add partition to PVs
pvcreate /dev/sdb2

# Create volume group
vgcreate -s 8M datastore /dev/sdb2

# Create logical volume
lvcreate -l 50 -n database datastore # 50 extents (400MiB)
lvcreate -L 400MiB -n database datastore

# Check results
pvdisplay
vgdisplay
lvdisplay

# Crete file system
mkfs.ext3 /dev/datastore/database

# Create mountpoint
mkdir /mnt/database

# Mount persistently
echo "UUID=$(lsblk --output=UUID /dev/datastore/database | tail -1) /mnt/database ext3 defaults 0 0" >> /etc/fstab
mount -a

# Check results
lsblk -f
df -hT
```


### Resize LV
```bash
# CHeck current LV size
lvdisplay
lvs

# Extend by 100 extents
lvextend -l 100 .r /dev/datastore/database

# Check results
lvdisplay
lvs
```


### Tune performance
```bash
# Ensure tuned is installed and runing
dnf install -y tuned
systemctl enable --now tuned

# See current profile
tuned-adm profile

# Set recommended profile manually..
tuned-adm recommend
tuned-adm virtual-guest
# ..or automatically
tuned-adm auto_profile

# Check results
tuned-adm profile
```  


### Build podman image from Containerfile
- USE SSH TO RUN AS USER. `SU - USER` is unreliable in this case
- ssh from other node
```bash
# Install required packages
dnf install podman
dnf groupinstall container-management

# When running rootless,...
systemctl --user enable --now podman.socket
# ..otherwise
systemctl enable --now podman.socket

# Build image
podman build --security-opt label=disable http://utility.domain16.example.com/rhel8/Containerfile
```
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    