# Configure LDAP authentication

### Question:
Bind with LDAP located on **ldap.server.com**
* LDAP dn is **dc=server,dc=com**
* certificate file is located on **http://ldap.server.com/pub/EXAMPLE-CA-CERT**
* **ldapuserX** should be able to log into your system, where **X** is server domain number but will have none home
directory (configured in different question)
* all **LDAP** users have password **password** 

### Note:
According to CertDepot, [Objectives around Virtualization and LDAP configuration are gone](https://www.certdepot.net/rhel8-rhcsa-whats-new/). Additionally, `authconfig-gtk` is no longer available for RHEL8. Instead, `authselect` should be used to configure the system to use SSSD, and SSSD configured to use LDAP, if LDAP authentication is required.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

LDAP is no longer part of the exam objectives. This question is therefore skipped. Better focus on the relevant parts.