#  Manage layered storage

### Question:
Create pool and filesystem for thin provisioning and snapshots.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Ensure stratis is installed and running
    ```
    dnf install -y stratis-cli stratisd
    systemctl enable --now stratisd
    ```
2. Check for available block devices `lsblk`
3. Create new stratispool and add devices
    ```
    stratis pool create pool1 /dev/sda1 /dev/sdb
    stratis pool add-data pool1 /dev/vdc
    ```
4. Create filesystem `stratis filesystem create pool1 fs1`
5. Check if successful
    ```
    stratis pool list
    stratis blockdev list pool1
    stratis filesystem list
    ```
6. Mount file system
```
echo "UUID=$(blkid -o value /dev/stratis/pool1/fs1 | head -1) /mnt/example xfs defaults,x-systemd.requires=stratisd.service 0 0" >> etc/fstab
mount -av
```

### BEWARE
- If you do not include the **x-systemd.requires=stratisd.service** mount
option in the /etc/fstab file for each Stratis file system, then the machine fails to
start correctly, and aborts to emergency.target the next time that you reboot it.
- `df` command reports that any mounted Stratis-managed XFS file system
is 1 TiB, regardless of the current allocation