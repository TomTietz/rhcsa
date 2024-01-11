#  Find files with specific properties

### Question:
Find All Files in ***/etc*** (not subdirectories) that where modified more than 180 days ago. And copy all of them to a directory /var/tmp/pvt

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Create directory for copies `mkdir -pv /var/tmp/pvt/`
2. Run copy command on every entry you can find with `find`
    ```
    find /etc -maxdepth 1 -type f -mtime +180 -exec cp {} /var/tmp/pvt \;
    ```
