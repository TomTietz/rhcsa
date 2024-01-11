# Reduce the size of existing logical volume

### Question:
Reduce the size of existing **logical volume** by **400MB**.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Show all extisting LVs `lvdisplay`
2. Unmount volume `unmount /MOUNTPOINT`
3. Shrink LV and resize file system accordingly `lvreduce -L -400MB /dev/datacontainer/datacopy --resizefs`
4. Remount volume `mount -a`