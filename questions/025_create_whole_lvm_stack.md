# Create whole LVM stack

### Question:
Create a new physical volume with volume group in the name of **datacontainer**, the extent of VG should be **16MB**.
Also create new logical volume with name **datacopy** with the size of  **50 extents** and filesystem **vfat** mounted
under **/datasource**. 

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Initialize a device as an LVM device /dev/sdc2 (example) `pvcreate /dev/sdc`
2. Create volume group `vgcreate datacontainer /dev/sdc -s 16MB`
3. Create new logical volume in the datacontainer VG `lvcreate --name datacopy --extents 50 datacontainer`
4. Create a vfat file system on the new LV `mkfs.vfat /dev/datacontainer/datacopy`
5. Mount file system persistently
    ```
    mkdir /datasource
    echo "UUID=$(blkid -o value /dev/datacontainer/datacopy | head -1) /datasource vfat defaults 0 0" >> /etc/fstab
    mount -av
    ```