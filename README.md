# Linux Adminstration Basics

This repository contains notes on the most common tasks on Linux systems and is loosely based on the requirements for Red Hat Certified System Adminstrator certificate. Some sections are therefore specific to RPM-based distros such as RHEL, Fedora or CentOS but most of these commands work on most Linux distros.

- [Linux Adminstration Basics](#linux-adminstration-basics)
  - [Manage Files from the Command Line](#manage-files-from-the-command-line)
  - [Create, View, and Edit Text Files](#create-view-and-edit-text-files)
  - [Manage Local Users and Groups](#manage-local-users-and-groups)
  - [Control Access to Files](#control-access-to-files)
  - [Monitor and Manage Linux Processes](#monitor-and-manage-linux-processes)
    - [Useful shortcuts in top](#useful-shortcuts-in-top)
    - [Monitor process states with $top or $ps aux](#monitor-process-states-with-top-or-ps-aux)
    - [Send process signals with the kill-command or top (Press *k*)](#send-process-signals-with-the-kill-command-or-top-press-k)
  - [Control Services and Daemons](#control-services-and-daemons)
  - [Configure and Secure SSH](#configure-and-secure-ssh)
  - [Manage Networking](#manage-networking)
    - [NetworkManager](#networkmanager)
  - [Install and Update Software Packages](#install-and-update-software-packages)
  - [Important Files](#important-files)
    - [Log Files](#log-files)
    - [Users, groups and Authentication](#users-groups-and-authentication)
    - [SSH](#ssh)
    - [Network](#network)
    - [Storage](#storage)
    - [Other](#other)
    - [Webserver](#webserver)


## Manage Files from the Command Line
- renaming files
    - **$ rename *expression* *replacement* *files***
- copy files from and to ssh hosts
    - **$ rsync *user*@*host*:*source_path* *dest_path***
    - or **$ rsync *source_path* *user*@*host*:*dest_path***

## Create, View, and Edit Text Files
- Create, view, and edit text files from command output or in a text editor.
    - **$ touch *file***
    - **$ cat *file***
    - **$ tail -5 *file***
    - **$ head -n 20 *file***
    - **$ grep *expression* *file***

## Manage Local Users and Groups
- Switch to the superuser account to manage a Linux system, and grant other users superuser access through the sudo command.
    - **$ sudo -i** or **$ su -**
    - the latter requires the root pw and the first sudo privilege
    - to add sudo pivilege to user: $usermod -aG wheel *user*
- Create, manage, and delete local user accounts.
    - **$ useradd [options] *user***
    - **$ usermod [options] *user***
    - **$ userdel [options] *user***
- Create, modify, and delete local group accounts.
    - **$ groupadd [options] *group***
    - **$ groupmod [options] *group***
    - **$ groupdel [options] *group***
- Set a password management policy for users, and manually lock and unlock user accounts.
    - usermod options for pw and account policies
        - Account expiration date: -e, --expiredate EXPIRE_DATE
        - Lock users password: -L, --lock
        - Lock full account: --expiredate 1
        - Unlock user password: -U, --unlock
    - Default behaviour and more details in /etc/shadow file (see **$ man 5 shadow**)

## Control Access to Files
- get/set Linux file-system permissions on files and interpret the security effects of different permission settings.
  - show permissions $ls -l

| Permission | Effect on Files | Effect on Directories | Command to Set the Permission |
|------------|-----------------|-----------------------|-------------------------------|
| **Read (r)** | Allows a file to be read. | Allows the contents of a directory to be listed if the execute permission is also set. | `chmod u+r filename` or `chmod u+r directoryname` |
| **Write (w)** | Allows a file to be modified. | Allows files within a directory to be created, deleted, and renamed if the execute permission is also set. | `chmod u+w filename` or `chmod u+w directoryname` |
| **Execute (x)** | Allows a file to be executed. | Allows a directory to be entered (i.e., `cd directoryname`). | `chmod u+x filename` or `chmod u+x directoryname` |

| Special Permission | Effect on Files | Effect on Directories | Command |
|------------------------|-----------------|-----------------------|------------------------|
| **Setuid (s)** | File executes as the owner of the file, not as the user who started it. | No effect. | `chmod u+s filename` |
| **Setgid (s)** | File executes with the permissions of the group. | New files created within the directory will inherit the group ID of the directory, not of the creator. | `chmod g+s directoryname` |
| **Sticky Bit (t)** | No effect. | Prevents users from deleting or renaming files in the directory unless they own the file or directory. | `chmod +t directoryname` |

- Control the default permissions of user-created files, explain the effects of special permissions, and use special and default permissions to set the group owner of files that are created in a directory.
  - initial octal permissions are 0777 (drwxrwxrwx) for directores and 0666 (-rw-rw-rw-) for regular files
  - shell session sets a umask to further restrict the initial permissions of a file
    - to show umask $umask
    - default value stored in /etc/login.defs and/or .bashrc
    - change (for session) with $umask *octal*


## Monitor and Manage Linux Processes
- process nice value = priority of a process
  - ranges from -20 (highest priority) to 19 (lowest priority)
  - setting initial nice value $nice -n <nice_value> <command>
  - changing nice value $renice <new_nice_value> -p <PID>

- Monitor processes with $top
  - summary with system load avg over the last 1, 5 and 15 minutes
  - Display running processes (with nice value, PID, state etc.), cpu usage, memory usage...

### Useful shortcuts in top

| Key | Action |
|-----|--------|
| `h` | Display help screen |
| `k` | Kill a process. You'll need to enter the process ID and signal to send. |
| `r` | Renice a process. You'll need to enter the process ID and the new nice value. |
| `u` | Display only processes from a specific user. |
| `M` | Sort processes by memory usage. |
| `P` | Sort processes by CPU usage. |
| `T` | Sort processes by time usage. |
| `c` | Display absolute path of the command for each process. |
| `t` | Toggle between summary information and task/cpus states. |
| `m` | Toggle between summary information and memory information. |
| `f` | Enter the field select screen. |
| `o` | Enter the sort field screen. |
| `l` | Toggle summary information on/off. |
| `z` | Toggle color/mono. |

### Monitor process states with $top or $ps aux

| Name | Flag | Kernel-Defined State Name | Description |
|------|------|---------------------------|-------------|
| Running | `R` | `TASK_RUNNING` | The process is either running or ready to run. |
| Interruptible Sleep | `S` | `TASK_INTERRUPTIBLE` | The process is waiting for an event to complete (such as input from the user). It can be awakened by signals. |
| Uninterruptible Sleep | `D` | `TASK_UNINTERRUPTIBLE` | The process is waiting for an event to complete, such as disk I/O. It cannot be awakened by signals. |
| Stopped | `T` | `TASK_STOPPED` | The process has been stopped, usually by receiving a signal. |
| Zombie | `Z` | `EXIT_ZOMBIE` | The process has completed execution but still needs its exit status to be read by another process. |
| Dead | `X` | `EXIT_DEAD` | The process is in the process of being removed from the system. |

### Send process signals with the kill-command or top (Press *k*)

| Name | Number | Description | Command |
|------|--------|-------------|---------|
| `SIGHUP` | `1` | Hangup detected on controlling terminal or death of controlling process. | `kill -1 PID` |
| `SIGINT` | `2` | Interrupt from keyboard (Ctrl+C). | `kill -2 PID` |
| `SIGQUIT` | `3` | Quit from keyboard (Ctrl+\\). | `kill -3 PID` |
| `SIGKILL` | `9` | Kill signal. Can't be caught, blocked, or ignored. | `kill -9 PID` |
| `SIGTERM` | `15` | Termination signal. This is the default signal sent by the `kill` command. | `kill -15 PID` |
| `SIGCONT` | `18` | Continue if stopped. | `kill -18 PID` |
| `SIGSTOP` | `19` | Stop process. Can't be caught, blocked, or ignored. | `kill -19 PID` |
| `SIGTSTP` | `20` | Stop typed at terminal (Ctrl+Z). | `kill -20 PID` |


## Control Services and Daemons
- **Monitor services** and system daemons with the systemd service.
  - show all service units $systemctl list-units --type=service (--all)
  - check if service is active **$ systemctl is-active sshd.service**
  - check if service is enabled **$ systemctl is-enabled sshd.service**
  - check if service is failed **$ systemctl is-failed sshd.service**
  - show systemd dependencies **$ systemctl list-dependencies**
  - details on systemd daemon **$ systemctl status *service***
- Control system daemons and network services with the systemctl command.
  - stop service **$ systemctl stop *service_name***
  - start service **$ systemctl start *service_name***
  - enable/disable service **$ systemctl stop *service_name***


## Configure and Secure SSH
- Configure secure command-line service on remote systems with OpenSSH.
  - ensure sshd daemon is active `systemctl start sshd.service`
  - test conection to server `ssh user@address`
  - generate local key `ssh-keygen`
  - add key to accepted keys on remote system `ssh-copy-id [-i <identitity_file>] user@address`
  - connect to server with private key authentication `ssh -i <identitity_file> user@address`
- configure the ssh daemon
  - configuration stored in */etc/ssh/sshd_config* or as drop ins in */etc/ssh/ssh_config.d/*
  - eg. disable direct root login with `PermitRootLogin no`
- user ssh configuration is stored in *~/.ssh/config*


## Manage Networking
- Network interfaces
  - network interface names start with the type: Ethernet(en/eth), WLAN(wl) or WWAN(ww), Loopback(lo)
  - show interfaces with `ip link show`, `ifconfig -a`, `netstat -i`
- IP addresses
  - show addreses with `ip addr show`
- General networking tools
  - test connection to remote system: `ping` or `ping6`
  - show rout packets take to a remote system `traceroute` or `traceroute6`
  - `mtr` combines the functionality of the traceroute and ping
  - in-depth node/network scans including open ports and operating system `nmap`
  - investigate sockets with `ss` (more usefully `ss -plunt`)
- Network config files
  - see confs.md


### NetworkManager
- network configuration daemon
- can be interfaced through cli (`nmcli`), interactive cli (`nmtui`) or web console (http://<ip_addr>:9090)
- backend
  - settings are summarized into profiles
  - one interface can only have one profile at the time (and vice versa)

| Command | Description |
|---------|-------------|
| `nmcli` | Display a summary of network settings and device status. |
| `nmcli device` | Show all devices. |
| `nmcli device show` | Show detailed information about all devices. |
| `nmcli connection` | Show all connections. |
| `nmcli connection up id <connection_name>` | Activate a connection by its name. |
| `nmcli connection down id <connection_name>` | Deactivate a connection by its name. |
| `nmcli connection add type ethernet con-name <connection_name> ifname <interface_name>` | Add a new Ethernet connection. |
| `nmcli connection modify id <connection_name> ipv4.method manual ipv4.addresses <IP_address>/<subnet_mask> ipv4.gateway <gateway_IP> ipv4.dns <DNS_IP> ipv4.method manual` | Set a static IP for a connection. |
| `nmcli connection modify id <connection_name> ipv4.method auto` | Set a connection to use DHCP. |
| `nmcli connection delete id <connection_name>` | Delete a connection. |
| `nmcli general hostname <new_hostname>` | Change the system's hostname. |


## Install and Update Software Packages
- **RPM**
  - a low-level tool
  - can **retrieve information** about the contents of package files and installed packages
  - per default gets information from a local database of installed packages
  - `rpm -qa` : List all installed packages.
  - `rpm -qf FILENAME` : Determine which package provides FILENAME
  - `rpm -qi PACKAGENAME` : Get detailed package information
  -  `rpm -qc PACKAGENAME` : List only the configuration files that the package installs
  -  `rpm -qd PACKAGENAME` : List only the documentation files that the package installs
  -  `rpm -qlp FILENAME` : List the files that the local package installs
     -  the `-p` option is used when inspecting local packages (usually downloaded through third party sources)
     -  for further inspection use the `rpm2cpio` command to list package contents: `rpm2cpio httpd-2.4.51-7.el9_0.x86_64.rpm | cpio -idv`
  -  can also be used to **install local packages** with ` rpm -ivh`
  -  NOT designed to work with package repositories or to resolve dependencies from multiple sources automatically
- DNF ((Dandified YUM))
  - install, update, remove, and get information about software packages and their dependencies
  - `dnf list [EXPRESSION]` : display installed and available packages
  - `dnf search KEYWORD` : lists packages by keywords that are in the name and summary fields only
  - `dnf search all KEYWORD` : lists packages by keywords that are in the name, summary or description
  - `dnf info PACKAGENAME` : detailed info about a package
  - `dnf provides FILEPATH` : displays packages that manage files that match the specified path name (path often include wildcard chars)
  - `dnf install PACKAGENAME` : install package
  - `dnf remove PACKAGENAME`: uninstall package
  - `dnf update [PACKAGENAME]` : install updates
  - `dnf check-update PACKAGENAME` : check for available package upgrades 
  - DNF supports **groups** = collections of related software that are installed together
    - `dnf group install/remove/list/search...`
  - DNF commands can are stored in **history** and can be reversed
    - `dnf history` : history of DNF commands
    - `dnf hitory undo ID` : undo a specific DNF command
  - DNF allows the usage of both official and third-party **repositories**
    - `dnf repolist [all]` : show available repos. Without all only activated ones
    - `dnf config-manager --enable REPONAME` : enable and disable repositories
    - `dnf config-manager --add-repo="REPOURL"` : add repo
    - `dnf config-manager --disable REPONAME`

## Important Files

This section summarizes the most important configuration and log files on Linux Systems. While some file are specific to rpm-based Ditros like RHEL or Fedora, most of these files are found on all mainstream Linux distros.

### Log Files
| File | Description |
|---|---|
| /var/log/messages | Most syslog messages except debug and special cases below |
| /var/log/secure | Syslog messages about security and authentication events |
| /var/log/maillog | Syslog messages about the mail server |
| /var/log/cron | Syslog messages about scheduled job execution |
| /var/log/boot.log | Non-syslog console messages about system startup |
| /run/log/journal/* | Default (non-persistent) journald logs |
| /var/log/journal/* | Persistent journald logs (only available when activated) |

### Users, groups and Authentication
| File           | Description                            | Documentation | Owner | Readable |
|----------------|----------------------------------------|---------------|-------|----------|
| /etc/passwd    | User account information               | passwd(5)     | root  | yes      |
| /etc/shadow    | Secure user account information.       | shadow(5)     | root  | no       |
| /etc/group     | System group information               | grou(5)       | root  | yes      |
| /etc/gshadow   | Secure system group information        | gshadow(5)    | root  | no       |
| /etc/sudo.conf | Configure the sudo front end           | sudo.conf(5)  | root  | no       |
| /etc/sudoers   | users and groups allowed to sudo & more| sudoers(5)    | root  | no       |

### SSH
| File | Description | Documentation |
|---|---|---|
| /etc/ss/sshd_config | SSH daemon config | sshd_config(5) |
| /etc/ssh/ssh_config | system-wide SSH config | ssh_config(5) |
| /home/\$USER/.ssh/config | user SSH config | ssh_config(5) |
| /home/\$USER/.ssh/authorized_keys | public keys allowed to authorize as $USER | ssh(1) |
| /home/\$USER/.ssh/known_hosts | public keys off all known hosts, warn or block if key changes | ssh(1) |



### Network
| File | Description | Documentation |
|---|---|---|
| /etc/resolv.conf /etc/hosts.conf /etc/nsswitch.conf | DNS config (ik, it [sucks](https://unix.stackexchange.com/a/681178)) | resolv.conf(5) hosts.conf(5) nsswitch.conf(5) |
| /etc/hosts | Hostnames and their IP addresses | hosts(5) |
| /etc/networks | Known networks and their names | networks(5) |
| /etc/services | Internet network services list | services(5) |
| /etc/NetworkManager/system-connections/* | each nm connection has a file here | nm-settings(5) |
| /etc/NetworkManager/NetworkManager.conf | nm config file | NetworkManager.conf |

### Storage

| File | Description |
|---|---|
| /etc/fstab | devices and their persistent mount points |

### Other
| File | Description |
|---|---|
| /etc/sysctl.conf | Linux kernel parameters at runtime | 
| /etc/crontab | commands and the times and dates at which they should be run. |
| /etc/environment | system-wide environment variables set at login |
| /etc/default/ | default values for various commands such as `useradd` and `grub` |
| /etc/rsyslog.conf | rsyslog daemon config, eg. where to send logs |
| /etc/systemd/journald.conf | journald config |
| /etc/chrony.conf | chronyd config for RTC and NTP sync |

### Webserver
| Directory | Description |
|---|---|
| /usr/share/nginx/html/ /usr/share/nginx/www /var/www/html | Possible directories for nginx public web content |
| var/www/ | Apache default directory |
