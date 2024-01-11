# Create users and assign them to secondary groups

### Question:
* Create group ***sysgrp*** 
* Create user **andrew** and **susan** who belong to this group (as secondary group)
* Create user **sarah** who does not have an access to the shell 
* Password for all the users should be **Postroll**

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Create group `groupadd sysgrp`
2. Create the users
    ```
    useradd -G sysgrp andrew
    useradd -G sysgrp susan
    useradd -s /user/sbin/nologin sarah
    ```
3. Set password for all users (safer than the -p option)
    ```
    passwd andrew
    passwd susan
    passwd sarah
    ```
4. Check if they are set correctly
    ```
    id andrew
    id susan
    id sarah
    ```