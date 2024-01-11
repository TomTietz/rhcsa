# Add entry to cron

### Question:
Create a cron job running as ***root***, starting at ***11PM every day*** and writing a report on daily system resource consumption in the **/var/log/consumption.log** file.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

```
su -              # or sudo -i
crontab -l        # check for existing crontab tasks 
crontab -e        # edit current crontab

## Add the following line
0 23 * * *            /usr/bin/sar -A > /var/log/consumption.log
# crontab entries should follow the following syntax
# field          allowed values
# -----          --------------
# minute         0-59
# hour           0-23
# day of month   1-31
# month          1-12 (or names, see below)
# day of week    0-7 (0 or 7 is Sunday, or use names)
# conmmand       whatever
```


### Additional comment:

The log file does not need to exist - it will be created automatically.
Of course **root** user can edit crontab of every user with flag **-u** specifying user name.
It is worth remembering that there are **/etc/cron.allow** and **/etc/cron.deny**. 
