# Set new default system level

### Question:
Set the default target to boot into **X Window level**.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHHEL 9)

1. Use systemd to configure default target `systemctl set-default graphical.target`
2. Check if it succeeded with `systemctl get-default`
3. Reboot system `systemctl reboot`