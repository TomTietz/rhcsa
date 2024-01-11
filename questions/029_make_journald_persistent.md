# Make journald persistent between reboots

PURE RHCSA8 QUESTION!

### Question:
Configure **journald** to persist between reboots

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL)

1. Open the journald config `vim /etc/systemd/journald.conf`
2. Edit the Storage line like this
    ```
    Storage=persistent
    ```
3. Restart journald `systemctl restart systemd-journald.service`
4. Data should now persistently be stored in *var/log/journald*


## Additional comment
Setting *Storage* to "auto" behaves like "persistent" if the /var/log/journal directory exists, and "volatile" otherwise (the existence of the directory controls the storage mode). Check ´man 5 journald` for more 