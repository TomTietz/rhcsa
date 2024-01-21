# Configure autofs

Question is related to question **21**.

### Question:
Configure **autofs** to automount the home directories of LDAP users.
* classroom.example.com (172.25.254.254) NFS-exports **/home/guests** to your system, whereX is Your server number
* LDAP userX home directory is **classroom.example.com:/home/guests/ldapuserX**
* **ldapuserX** home directory should be automounted locally beneath **/home** as **/home/guests/ldapuserX**
* home directories must be writable by their users
* while You are able to login as any of the users **ldapuser1-20** the only home directory You are able to access is **ldapuserX**
 

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

LDAP is no longer part of the exam objectives. This question is therefore skipped. Better focus on the relevant parts.