# Add new remote repository for YUM

### Question:
Add additional repository for DNF with name **my_custom_repo** which can be found via URL **http://local.repo/rhel9**

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL)

1. Create new repo file in */etc/yum.repos.d/* (still not renamed to dnf ik)
    ```
    echo "[my_custom_repo]
          name=my_custom_repo
          baseurl=http://local.repo/rhel9
          enabled=1
          gpgcheck=0" \
          >> /etc/yum.repos.d/my_custom_repo.repo
    ```
2. Check if new repo has been added `dnf repolist all`