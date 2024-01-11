# Assign SELinux context

### Question:
Assign the same **SELinux** contexts used by the home directories to the **/xfs** directory permanently.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

* For interacting with **context** in **SELinux** we need a command that is not available by default on RHEL. Therefore we have to install it. First we find out
what is the package name we need:

```
yum whatprovides */semanage
```

* On my system (CentOS) it was **policycoreutils-python** with additional architecture/version suffix. This is what must be installed using **YUM**.

1. Ensure `semanage` exists
    ```
    dnf provides /usr/sbin/semanage
    dnf install /usr/sbin/semanage
    ```
2. Check home directories and what fcontext they use `ls -lZ /home`
3. Set label for /xfs the same as /shome/home1 (multiple ways)
    ```
    semanage fcontext -a -t :user_home_dir_t /xfs      # directly with label
    semanage fcontext -a -e /home/home1 /xfs           # equal as other dir

    restorecon -R /xfs
    ```