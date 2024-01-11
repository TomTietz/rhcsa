# Create new filesystem on logical volume

### Question:
Create a ***xfs*** file system on a new logical volume of ***100MB*** called ***lv_xfs***. Mount it permanently with uuid under **/xfs**.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Check for existing VG and create if necessary
    ```
    vgdisplay

    # if no Vg exists
    pvcreate /dev/bla /dev/blub
    vgcreate VGNAME /dev/bla /dev/blub
    ```
2. Create new logical volume `lvcreate -L 100MB --name lv_xfs /dev/VGNAME`
3. Create the XFS filesystem `mkfs.xfs /dev/VGNAME/lv_xfs`
4. Mount file system persistently
    ```
    mkdir /xfs
    echo "/dev/VGNAME/lv_xfs /xfs xfs defualts 0 0" >> /etc/fstab
    mount -v /xfs
    ```


### Additional comment:

Make sure that You know the difference between **partition label** and **file system name** - https://superuser.com/questions/1099232/what-is-the-difference-between-a-partition-name-and-a-partition-label/1099292
and You do not confuse them.

When editing **fstab** file by hand we got much more control over mount options. Also pay attention to the fact that with provided solution we use '1 2' values for 
checking mount point integrity - the first one indicates how often the partition will be backed by **dump** program, the second one points to the level of integrity check when
mounting (0 means no check, 1 is applied only to the root partition).