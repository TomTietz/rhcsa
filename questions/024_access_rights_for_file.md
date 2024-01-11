# Configure access rights to the file

### Question:
Copy the file **/etc/fstab** to **/var/tmp**. The file copied should be owned by **root**, the group also should be set to **root**
and should not be executable by anyone.
The user **andrew** should have read&write access to this file.
User **susan** should not have any rights to this file.
All other users (current or future) should have the ability to read this file.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Create copy of */etc/fstab* `cp /etc/fstab /var/tmp/`
2. Ensure root is the onwer and group of the copy `chown root:root /var/tmp/fstab`
3. Set permisison bits accordingly `chmod 664 /var/tmp/fstab`
4. Add ACL entries
    ```
    setfact -m u:andrew:rw /var/tmp/fstab
    setfacl -m u:susan:- /var/tmp/fstab
    ```
5. Check access
    ```
    ls -l /var/tmp/fstab
    getfacl /var/tmp/fstab
    ```