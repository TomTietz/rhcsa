# Extend existing logical volume by 200MB and add label to it 

### Question:
Extend the existing ***xfs*** file system to a total size of ***200MB*** and add a label called ***myFS***.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL9)

1. Get overview of devices, partituions, PVs, VGs, and LVs
    ```
    lsblk             # show devices and partitions
    pvdisplay         # show physical volumes included in LVM 
    vgdisplay         # show volume groups included in LVM 
    lvdisplay         # show logical volumes included in LVM 
    ```
2. Extent an LV (`-r` extends the file system too)
    ```
    lvextend -r --size 200MB LV    # extend to 200MB
    lvextend -r --size +200KiB LV  # inccrease size by 200KiB
    lvextend -r LV PV   # extend to the full available size of physical volume PV
    ```
3. Relabel file system
    ```
    umount /LINK/TO/FILESYSTEM/MOUNT/POINT
    xfs_admin -L "myFS" /dev/VOLUME_GROUP/LOGICAL_VOLUME
    mount /LINK/TO/FILESYSTEM/MOUNT/POINT
    ```


### Additional comment
In case you forgot to specify the `-r` option you can grow the filesystem with `xfs_growfs [ mount-point | block-device ]`