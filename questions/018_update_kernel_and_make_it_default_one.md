# Install new kernel and make it default

### Question:
Install the kernel from the source http://some.link/to/kernel. The following criteria must be met:
* installed kernel will be the default one when system boots
* previous kernel is still available

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Install a repository providing the new kernel as described in the answer for Question 016
2. Install new kernel `dnf install -y kernel`
3. Check if kernel can be found under */boot* `ls -l /boot`
   - ususally kernels are stored there
   - if not find the kernel and move it to the */boot* directory
4. The kernel should be listed when running `grubby --info=ALL`
    - otherwise add kernel to grub `grubby --add-kernel=/boot/kernel`
5. Set default kernel
    ```
    grubby --set-default=/boot/kernel     # by path
    grubby --set-default-index KERNEL_ID  # by index
    ```
6. Reboot system `systemctl reboot`