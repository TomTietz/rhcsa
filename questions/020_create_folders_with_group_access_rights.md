# Create folder with group access rights

### Question:
Create a collaborative directory **/redhat/sysgrp** with following properties:
* group ownership of the folder is for group **sysgrp**
* members of **sysgrp** should have full access to this folder but no other user
* files created in this folder have by default group access to **sysgrp** 

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Ensure the group sysgrp exists `cat /etc/group | grep sysgrp`
2. Create directory `mkdir -pv /redhat/sysgrp`
3. Set group ownership `chown root:sysgrp /redhat/sysgrp`
    - setting owner to root makes sure no user from the group has owner permisison and we can set owner perm. to 0
4. Set permission including GID `chmod 2070 /redhat/sysgrp`