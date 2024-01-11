# Install Apache and allow it to get documents from NFS mounted folder 

### Question:
Install Apache and allow it to get documents from NFS mounted folder (**SELinux** part)

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Install Apache and NFS `dnf install -y httpd nfs-utils`
2. Add firewall rule for web server and nfs
    ```
    firewall-cmd --permanent --add-service=http --add-service=nfs
    firewall-cmd --reload
    ```
3. Start Apache daemon `systemctl enable --now httpd`
4. Start NFS client `systemctl enable --now nfs-client`
5. Mount NFS folder
    ```
    echo "serveraddr:/path/of/export /path/of/mount nfs rw,sync 0 0" >> /etc/fstab
    mount /path/of/mount
    ```
6. Allow NFS folder to be accessed by web server
    ```
    # Check if boolean is set
    getsebool httpd_use_nfs
    getsebool -a | grep -i nfs    # if unsure what the boolean is called

    # Set boolean
    setsebool -P httpd_use_nfs 1
    # use semanage for persistant after reboot,
    semanage boolean --on --modify httpd_use_nfs
    ```
7. Enable SELinux  `setenforce Enforcing`
8. Configure Apache to use NFS folder as home directory as per [Apache documentation](https://httpd.apache.org/docs/2.4/urlmapping.html)
9. Reload Apache `systemctl reload httpd`



### Additional comment:

**Apache** knowledge is not in the requirements for the exam. However it is very popular server and also serves additional purposes here - 
it gives an opportunity to use **yum**, configure **firewall** with **systemd service** and finally configure **SELinux**.
<br />
