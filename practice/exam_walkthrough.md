# Exam Walkthrough

Disclaimer: There is no single EX200 exam and learning a single solution by heart is pointless. This document merely outlines an examplorary exam to provide an understanding of the scope of what is required of you in the exam. Follow along on the keyboard for optimal learning.


## Initial Setup



## Walkthrough

### Repair Boot
**Problem/Task:** Broken boot and unknown root password

**Solution:**
1. Interrupt boot by pressing any button
2. Edit boot parameter
3. append `rd.break` to kernel line
4. Continue with CTR+X und 
5. `mount -o remount,rw /sysroot`
6. `chroot /sysroot`
7. `passwd`
8. `touch /.autorelabel`
9. 2x`exit`

### Add Repo
**Problem/Task:** dnf/yum repolist does not return results

**Solution:**
1. Add new repo `dnf config-manager --add-repo http://example.com/different/repo`
2. Check again `dnf repolist --all`
3. Enable if necessary `dnf config-manager --set-enabled <repoid>`

### Create Static Connection
**Problem/Task:** Server has an automatic (DHCP) ipv4 configuration but needs a manual one

**Solution:**
1. List interfaces `ip a s`
2. Show connections `nmcli c s`
3. Add connection with manual values
```bash
nmcli con add \
    con-name staticp \
    ifname eth0 \
    ipv4.method manual \
    ipv4.addresses manual \
    ipv4.dns manual \
    ipv4.gateway manual \
    ipv6.method auto \
    connection.autoonnect yes
```
4. Start up connection `nmcli con up staticp`


### NTP Client
**Problem/Task:** The server needs to be configured as an NTP client

**Solution:**
1. REMEMBER: NTP is managed by **chrony**
2. `vim /etc/chrony.conf`
    - edit top (non-comment) line to be `server classroom.example.com iburst`
3. Enable and restart service `systemctl enable chronyd && systemctl restart chronyd`

### Access Control Lists
**Problem/Task:** A specific file needs the following permissions:
- *lisa* can read and write
- *simon* has no access at all
- everyone else can read 

**Solution:**
1. Get current ACLs `getfacl FILE`
2. Set ACLs
```bash
setfacl -m d:u:r-- FILE
setfacl -m u:lisa:rw- FILE
setfacl -m u:simon:--- FILE
```


### Create User
**Problem/Task:**  More users and groups are needed. One user should not be able to login. Two users should have the created group as their secondary group.

**Solution:**
1. Create the users
```bash
groupadd people
useradd -aG people horst
useradd -aG people rainer
useradd -s /usr/sbin/nologin harry
```
2. Set their password `passwd USER`
3. Check results `getent passwd USER`


### Automount Homes
**Problem/Task:** Remote users should automatically get a home directory nmounted at */netuser/*
- the diretorsy to mount is exported at *utility.network14.example.com/remoteuser/*

**Solution:**
1. Ensure NFS is installed `dnf install nfs-utils`
2. Ensure directory exists `mkdir /netuser`
3. Create autofas master file `echo "/netuser  /etc/homes.map" >> /etc/auto.master.d/homes.autofs`
4. Create mapping file `echo "* -rw,snyc,fstype=nfs4 utility.network14.example.com/&" > /etc/homes.map`
5. Start the autofs service `systemctl enable --now autofs && systemctl restart autofs`
6. Set required SELinux policy `semanage boolean --modify --on use_nfs_home_dirs`
7. Check with `mount -v`


### Swap Partition
**Problem/Task:** We are running out of memory and need swap space as a temporary solution. Create a new partition and use it as swap space. 

**Solution:**
1. Create new partition
```bash
parted /dev/sdc mkpart primary linux-swap 1024KB 512MB
udevadm settle
```
2. Create swap file system on the partition `mkswap /dev/sdc1`
3. Get UUID of partition `lsblk --output=UUID /dev/sdc1`
4. Persistently mount swap partition 
```bash
vim /etc/fstab
# Append this line:
UUID=YOUR_UUID swap swap defaults 0 0
```
1. Turn swap on `swapon -a`


### Logical Volume Management
**Problem/Task:** Create a partition, add the partition and another block device as physical volumes to a volume group, create a logical volume and mount it at */logic*.

**Solution:**
1. Create partition
```bash
parted /dev/sdc mkpart primary 513MB 20GB
udevadm settle
```
2. Initialize device and partition as PVs `pvcreate /dev/sdc1 /dev/sdd`
    - This step is technically optional since `vgcreate` will do it for you
3. Create volume group `vgcreate myvg /dev/sdc1 /dev/sdd`
4. Create logical volume `lvcreate -L 1GB --name lv1 myvg`
5. Create file system on LV `mkfs.xfs /dev/myvg/lv1`
6. Mount LV
```bash
echo "UUID=$(lsblk --output=UUID /dev/myvg/lv1 | tail -1)  /logic xfs defaults 0 0" >> /etc/fstab
mkdir /logic
mount /logic
```

### Modify LV
**Problem/Task:** We create the LV from the previous task with too little space. Extend it by 2GB.

**Solution:**
1. Examine existing LVs
2. Extend logical volume `lvextend -L +2GB lv1`
3. Extend file file system on LV
```bash
# For ext2, ext3 and ext4 file systems
resize2fs /dev/myvg/lv1
# For xfs
xfs_growfs /logic
```


### Stratis Storage Management
**Problem/Task:** We want to pivot to Stratis storage management. Repeat the previous exercise but with startis instead of LVM.

**Solution:**
1. Ensure Stratis is installed `dnf install -y startisd stratis-cli`
2. Enable Stratis `systemctl enable --now stratisd`
3. Create Stratis pool `stratis pool create /dev/sdc1 /dev/sdd`
    - Check with `stratis pool list`
4. Create filesystem `stratis filesystem create pool1 fs1` (XFS only)
```bash
echo "UUID=$(lsblk --output=UUID /dev/stratis/pool1/fs1 | tail -1)  /stratis xfs defaults x-systemd.requires=stratisd.service 0 0" >> /etc/fstab
mkdir /stratis
mount /stratis
```
**ATTENTION:** the x-systemd.requires=stratisd.service option is crucial. System will boot into emergency.target otherwise

### Tune System Performance
**Problem/Task:** Due to the energy crisis you must persistently set the performance profile to optimize for power consumption

**Solution:**
1. List available profiles `tuned-adm list`
2. Show current profile `tuned-adm active`
3. Set profile `tuned profile powersave`


### Cron Job
**Problem/Task:** Create a crontab job that writes "Hello readers" to */home/simon/hello.out* every working day (Mon-Fri) at 18:09

**Solution:*
1. Ensure crond is running
```bash
systemctl status crond
systemctl enable --now crond
```
1. Add crontab entry
```bash
touch /etc/cron.d/hello
vim /etc/cron.d/hello
# Add the following line
09 18 * * 1-5 root echo "Hello log readers" >> /home/simon/hello.out
```
1. Reload crond `systemctl reload crond.service`


### Anacron
**Problem/Task:** Create a daily anacron job wiping */home/simon/hello.out*. Anacron is better suited for systems that re not always turned on.

**Solution:**
1. Add anacron entry
```bash
touch /etc/cron.daily/wipe
vim /etc/cron.daily/wipe
# Add the following line
echo "" > /home/simon/hello.out
```
1. Make it runnable `chmod +x /etc/cron.daily/wipe`


### Find
**Problem/Task:** Find all files belonging to user *simon* and copy them /tmp/simons_stuff/

**Solution:**
1. Create dir `mkdir /simons-stuff`
2. Run this command
```bash
find / -type f -user simon -exec cp '{}' /simons-stuff \; 
```


### Container
**Problem/Task:** Set up a webserver container as user lisa with persistent storage as a systemd service that automatically starts after restart

**Solution:**
1. Enable login liinger for *lisa* `loginctl enable-linger lisa`
2. Change to user *lisa* `su - lisa`
3. Login into podman registry `podman login registry.lab.example.com`
4. Run container from image
```bash
podman run -d --name webserver -v /home/lisa/webfiles:/usr/share/nginx/html -p 8080:8080 registry.lab.example.com/rhel9/nginx
```
5.. Set as systemd (user) service
```bash
mkdir -pv ~/.config/systemd/user
cd ~/.config/systemd/user
podman generate systemd --name webserver --files --new
podman stop webserver
podman rm webserver
```
6. Enable service `systemctl --user enable --now container-webserver.service`

**Comment:** If you want to run the container as root, put the service file into */etc/systemd/system* directory, skip the linger command and use systemctl without *--user*
