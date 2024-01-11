# Restore root password

It can be preliminary task for starting Your exam. It is crucial to know this procedure by heart. 

### Question:
 You do not know the root password but You have physical access to the machine. Create new root password
and log into the system.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL9)

1. Interrupt boot process with F2 or sth
2. Press e to edit startup attributes
3. Append rd.break at the line that starts with linux...
4. Press Ctrl+x to boot with the changes.
5. Press Enter to perform maintenance when prompted
6. Remount the file system `mount -o remount,rw /sysroot`
7. Switch into a chroot jail, where /sysroot is treated as the root of the file-system tree `chroot /sysroot`
8. Set root passwd `passwd root`
9. Ensure all unlabeled files, including /etc/shadow at this point, get relabeled during root `touch /.autorelabel`
10. Type ***exit*** twice (with pressing ENTER after each one)
11. Now You can log into the system using new password


### Additional comment:

It is possible to edit startup parameters of the kernel from the command line and make it persistent. Just edit **/etc/default/grub**
file and after that make sure to run ***grub2-mkconfig > /boot/grub2/grub.cfg*** in order to apply the changes (for old **BIOS** config). For the **UEFI** computers the final file is **/boot/efi/EFI/redhat/grub.cfg**.
