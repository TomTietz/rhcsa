# Enable SELinux in enforcing mode

### Question:
Enable SELinux in enforcing mode

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer:

1. Make sure SELinux is enabled `getenforce` or `grep -i enforcing /etc/selinux/config`
2. To change the enforcement mode either use `setenforce Enforcing` or edit */etc/selinux/config*
3. Reboot system `systemctl reboot`
4. Check enforcing mode with `getenforce`

### Additional Comment
You will see */etc/sysconfig/selinux* mentioned elsewhere. On modern systems this file is just a symbolic link to */etc/selinux/config*.