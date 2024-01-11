# Change hostname

### Question:
Change hostname of the system

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Show current hostname `hostname`
2. countless methods of changing the current hostname
    ```
    hostname NEW_HOSTNAME                  # not persistent
    echo NEW_HOSTNAME > /etc/hostname      # not recommended
    hostnamectl hostname NEW_HOSTNAME
    nmcli general hostname NEW_HOSTNAME
    ```
 


### Additional comment:

Of course hostname is being shown near prompt sign in the console, however there is also a couple of ways of obtaining it
is using ***hostnamectl*** which besides info about hostname gives a lot of other useful information
