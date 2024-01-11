# Create new physical partition and create filesystem on it

### Question:
Create new physical partition with 100MB size and mount it under **/meet**

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Look at available physical devices with space left for another partition
    - assuming an empty block device */dev/sdc* with an msdos partitionn table from here on...
2. Create new partition `parted /dev/sdc mkpart primary 1049kB 101MB`
3. Create XFS file system on partition `mkfs.xfs /dev/sdc1`
4. Mount new partition with UUID
```
lsblk --fs                                                          # get UUID of device
echo "UUID=UUID_OF_PARTITION /meet xfs defaults 0 0" /etc/fstab     # add entry in mount file
mkdir /meet                                                         # create mountpoint dir
mount /meet                                                         # mount file system
```
5. Check if action was successful `lsblk`