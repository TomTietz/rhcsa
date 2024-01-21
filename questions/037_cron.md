# Containers

### Question
Create a job using `at` to write "This task was easy!" to ~/at_job.txt in 10 minutes.
Create a job using `cron` to write "Wow! I'm going to pass this test!" every Tuesday at 3pm to /var/log/messages.
Make sure every the file /var/log/ssh_events_counter exists and contains a daily report consisting of the date and the number of lines in /var/log/messages containing the word "ssh"


***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. `echo "This task was easy!" >> ~/at_job.txt | at now +10min`
2. `sudo crontab -e`
   - append line `0 15 * * 2 root echo "Wow! I'm going to pass this test!" >> /var/log/messages`
3. `echo "grep ssh /var/log/messages | wc -l >> /var/log/ssh_events_counter" >> /etc/cron.daily/ssh_count`
4. `chmod +x /etc/cron.daily/ssh_count`