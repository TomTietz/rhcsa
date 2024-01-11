# Directory ownership by a group

User's creation was covered by question 008 

### Question:
Create a directory named ***/common***. Allow **john** and **davis** to share documents in the ***/common*** directory using a group called **team**.
Both of them can read, write and remove documents from the other in this directory but any user not member of the group can’t.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Create directory `mkdir /common`
2. Create group and add **john** and **davis** to it
    ```
    groupadd team
    usermod -aG team john
    usermod -aG team davis
    # same thing but as one command
    groupadd -U john,davis team
    ```
3. Change ownership and permissions of */common*
    ```
    chown :team /common
    chmod 2770 /common
    ```


### Additional comment:

It is possible to change user's primary group for the current session using **newgrp** command.

