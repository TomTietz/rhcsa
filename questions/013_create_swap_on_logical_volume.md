# Create swap partition

### Question:
Create a logical volume of **200MB** called **lv_swap2** and add it permanently to the current swap space.

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
2. Create new logical volume `lvcreate -L 200MB --name lv_swap2 /dev/VGNAME`
3. Create the swap filesystem `mkswap /dev/VGNAME/lv_swap2`
4. Turn swap on `swapon /dev/VGNAME/lv_swap2`
5. Turn on swap persistently
    ```
    echo "/dev/VGNAME/lv_swap2 swap swap defaults 0 0" >> /etc/fstab
    swapon -s
    ```


### Additional comment:

Both commands **mkswap** and **swapon** have parameters like **-L** and **-U** so it is possible to give these commands not only the
path (to the file or partition) but label or UUID.