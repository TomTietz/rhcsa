# Containers

### Question
1. Create a new user with the following attributes
```
username: steve
userid: 9766
group(s): wheel, db_admin, docker
password: 12345678!
homedir: /mnt/autofs_home/steve/
password-expiration: 1 month from now
```
2. Set the default expiration date for all new user passwords to 90 days with a 10 day warning period.
3. Apply the same rules to existing user *steve*



***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. The shell script for the groups should look something like this
```
#!/bin/sh
groups 
groupadd dba_admin
groupadd dba_managers
groupadd dba_staff
```

