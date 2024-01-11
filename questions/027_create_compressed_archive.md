# Create compressed archive

### Question:
Create the archive file **/root/local.tgz** for **/usr/local** compressed by **gzip**.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Run the tar command I guess
    ```
    tar -c -f /boot/local.tgz --gzip /usr/local/     # create archive
    tar --list -f /boot/local.tgz                    # check archive 
    ```