# Containers

### Question
Enable SSh access for root on both a server and enable key-based authentication from your workstation.
Make sure the SSH daemon runs after rebooting.


***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)#

1. On the server: Enable root login
   1. `echo "PermitRootLogin yes" > /etc/ssh/sshd_config.d/custom.conf`
   2. `systemctl restart sshd`
2. On the workstation: Generate key and send it to server
   1. `ssh-keygen`
   2. `ssh-copy-id root@SERVERADDR`
   3. Use password to authenticate
3. Prohibit password based root login 
   1. `echo "PermitRootLogin prohibit-password" > /etc/ssh/sshd_config.d/custom.conf`
   2. `systemctl restart sshd`