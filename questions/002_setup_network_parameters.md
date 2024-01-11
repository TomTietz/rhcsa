# Setup network parameters
### Question:
Please create new network connection using provided values:
IP:           some.ip.to.be.used/mask
Gateway:      some.gateway.to.be.used
DNS server:   some.dns.to.be.used
Interface:    eth0
***
(scroll down for an answer)
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Find out the name of the inteface for the connection `ip a s`
2. Create new connection using nmcli `nmcli connection add con-name some-name type ethernet ifname eno1 ipv4.method manual ipv4.addresses some.ip.to.be.used/mask ipv4.gateway some.gateway.to.be.used ipv4.dns some.dns.to.be.used `
3. Check iof connection was successfully added `nmcli c s`
4. Start up connection `nmcli connection up some-name`
5. Check if connection is up `nmcli con show --active`
6. To make things persistent:
   1. isable the old connection from starting on reboot `nmcli con mod old-connection connection.autoconnect no`
   2. automatically switch to new connection on reboot `nmcli con up MY_CONNECTION connection.autoconnect yes`
  
### Additional comment:
It is possible to edit existing connection using **nmtui** tool which can be easier. 
Also when using GUI there is also graphical interface for it.
