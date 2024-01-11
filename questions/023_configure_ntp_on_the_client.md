# Configure new NTP server

### Question:
Configure Your system that it is **NTP** client of **classroom.example.com** 

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Make sure chronyd is installed and enabled
    ```
    dnf install -y chrony
    systemctl enable --now chronyd.service
    ```
2. Edit the chronyd config with `vim /etc/chrony.conf`
3. Change the server line to
    ```
    server classroom.example.com iburst
    ```
4. Check if chrony updated successfully `chronyc sources -v`
5. Reload chrony daemon if necessary`systemctl reload chronyd`