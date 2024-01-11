# Linux Adminstration Basics

Notes on the most common tasks on Linux systems and is loosely based on the requirements for Red Hat Certified System Adminstrator certificate. Some sections are therefore specific to RPM-based distros such as RHEL, Fedora or CentOS but most of these commands work on most Linux distros.

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
  - [Improve Command-line Productivity](#improve-command-line-productivity)
  - [Schedule Future Tasks](#schedule-future-tasks)
  - [Analyze and Store Logs](#analyze-and-store-logs)
    - [Sample Rules of the rsyslog Service](#sample-rules-of-the-rsyslog-service)
    - [System Journal](#system-journal)
    - [rsyslog vs journald](#rsyslog-vs-journald)
    - [Maintain accurate time](#maintain-accurate-time)
  - [Archive and Transfer Files](#archive-and-transfer-files)
  - [Tune System Performance](#tune-system-performance)
  - [Manage SELinux Security](#manage-selinux-security)
  - [Manage Basic Storage](#manage-basic-storage)
  - [Manage Storage Stack](#manage-storage-stack)
  - [Access Network-Attached Storage](#access-network-attached-storage)
  - [Control the Boot Process](#control-the-boot-process)
  - [Manage Network Security](#manage-network-security)
  - [Containers](#containers)
  - [Important Files](#important-files)
    - [Log Files](#log-files)
    - [Users, groups and Authentication](#users-groups-and-authentication)
    - [SSH](#ssh)
    - [Network](#network)
    - [Storage](#storage)
    - [Other](#other)
    - [Webserver](#webserver)
    - [Binaries](#binaries)


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


## Improve Command-line Productivity
- Run commands more efficiently by using advanced features of the Bash shell, shell scripts, and various Red Hat Enterprise Linux utilities.
- Run repetitive tasks with for loops, evaluate exit codes from commands and scripts, run tests with operators, and create conditional structures with if statements.
- Create regular expressions to match data, apply regular expressions to text files with the grep command, and use grep to search files and data from piped commands.

## Schedule Future Tasks
- Schedule tasks to execute at a specific time and date.
- Set up a command to run once at a future time.
- Schedule commands to run on a repeating schedule with a user's crontab file.
    - check user is in /etc/cron.allow or not in /etc/cron.deny
    - doc for syntax $ man 5 crontab
    - Syntax: min h day-of-month month weekday command
- Schedule commands to run on a repeating schedule with the system crontab file and directories.
    - defined in etc/cron.d or etc/crontab (the latter explains the syntax too)
    - nowadays more often with anacrontab
        - runs the daily, weekly, and monthly jobs from the /etc/anacrontab configuration file
        - Files in the /var/spool/anacron/ directory determine the daily, weekly, and monthly jobs
- Enable and disable systemd timers, and configure a timer that manages temporary files.
    - systemd timer unit activates another unit of a different type (such as a service), whose unit name matches the timer unit name
    - have the .timer file ending
    -   [Timer]
        OnCalendar=*:00/10
    - doc: $ man 5 systemd.timer

## Analyze and Store Logs
- log files usually stored in /var/log/

| File | Description |
|---|---|
| /var/log/messages | Most syslog messages except debug and special cases below |
| /var/log/secure | Syslog messages about security and authentication events |
| /var/log/maillog | Syslog messages about the mail server |
| /var/log/cron | Syslog messages about scheduled job execution |
| /var/log/boot.log | Non-syslog console messages about system startup |

- log messages categorized by
  - **facility** (the subsystem that produces the message) and..
  - **priority** (the message'sseverity)
  
| Code | Facility | Facility Description |
|------|----------|----------------------|
| 0 | kern | Kernel messages |
| 1 | user | User-level messages |
| 2 | mail | Mail system messages |
| 3 | daemon | System daemon messages |
| 4 | auth | Authentication and security messages |
| 5 | syslog | Internal syslog messages |
| 6 | lpr | Printer messages |
| 7 | news | Network news messages |
| 8 | uucp | UUCP protocol messages |
| 9 | cron | Clock daemon messages |
| 10 | authpriv | Non-system authorization messages |
| 11 | ftp | FTP protocol messages |
| 16-23 | local0 to local7 | Custom local messages |

| Code | Priority | Priority Description |
|------|----------|----------------------|
| 0 | emerg | System is unusable |
| 1 | alert | Action must be taken immediately |
| 2 | crit | Critical condition |
| 3 | err | Non-critical error condition |
| 4 | warning | Warning condition |
| 5 | notice | Normal but significant event |
| 6 | info | Informational event |
| 7 | debug | Debugging-level message |

- rsyslog service uses the facility and priority of log messages to determine how to handle them
- rules for this stored in /etc/rsyslog.conf and any file in /etc/rsyslog.d with .conf extension
  - format: <facility>.<priority>    <log-file>

### Sample Rules of the rsyslog Service
```
#### RULES ####
# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.* /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none /var/log/messages

# The authpriv file has restricted access.
authpriv.* /var/log/secure
```

- analysing log messages
  - earliest message at the start and the latest message at the end of the log file
  - standard rsyslog format
    - time stamp eg. **Mar 20 20:11:48**
    - host that sends the log message eg. **localhost**
    - program or process name and PID number that sent the log message eg. **sshd[1433]**
    - message text eg. **Failed login..**
    - full example: *Mar 20 20:11:48 localhost sshd[1433]: Failed password for student from 172.25.0.10 port 59344 ssh2*
- manually send log messages with `logger -p <facility>.<priority> "<message>"` (default *user.notive* if no -p option)

### System Journal
- another logging service with slightly different features compared to `rsyslog`
  - both can coexist on the same machine without any problems
- journald is not persistent by default (logs stored in /run/log/journal)
  - to make persistent set `Storage=persistent` in */etc/systemd/journald.conf*
  - this change sthe log directory to */var/log/journal*
  - if `Storage=auto` the system acts persistent if */var/log/journal* exists
  - if `Storage=none` system drops all logs
- used with `journalctl`
  - `journalctl` : view all messages in the journal
  - `journalctl -n <number-of-messages>` : show last n messages
  - `journalctl -p <priority>` : show messages with specific prio
  - `journalctl -p -u <systemd-unit>` : show messages from specific systemd unit eg. sshd-service
  - `journalctl --since <time>` : show messages since time eg. today, 16:40...

### rsyslog vs journald

| Feature | journald | rsyslog |
|---------|----------|---------|
| Part of `systemd` | Yes | No |
| Structured logging | Yes | No |
| Indexing logs for fast search | Yes | No |
| Access control | Yes | No |
| Signed messages | Yes | No |
| Handles logs from services started by `systemd` | Yes | No |
| Handles logs from the earliest boot process messages | Yes | No |
| Collects logs from many sources | No | Yes |
| Handles logs from file sources | No | Yes |
| Reads from and writes to the journal | No | Yes |
| Handles a large volume of logs | No | Yes |

### Maintain accurate time
- System time synchronization is critical for log file analysis across multiple systems
- `timedatectl` : overview of the current time-related system settings (eg. current time, time zone, NTP synchronization)
  - `timedatectl list-timezones`
  - `timedatectl set-timezone <timezone>`
  - `timedatectl set-time <time>`
  - `timedatectl set-ntp [false|true]`
- **chronyd** service
  - tracks the usually inaccurate local Real-Time Clock (RTC) by synchronizing it to the configured NTP server
  - */etc/chrony.conf* contains settings about NTP server and more

## Archive and Transfer Files
- **tar** utility
  - common command to create, manage, and extract archives
  - create archive: `tar -c [-f ARCHIVE] [OPTIONS] [FILE...]`
  - extract archive `tar -x [-f ARCHIVE] [OPTIONS] [MEMBER...]`
  - list archive contents `tar -t [-f ARCHIVE] [OPTIONS] [MEMBER...]`
  - useful options
    - `-a` or `--auto-compress` : Use the archive's suffix to determine the algorithm to use
    - `--selinux` : Enable SELinux context support, and store SELinux file contexts
  - example command
    - `tar -ca --selinux -f archive.tar.gz *.md rh134/`
    - `tar -x -f archive.tar.gz`
- **sftp** (Secure File Transfer Protocol)
  - part of OpenSSH
  - secure authentication and encrypted data transfer to and from the SSH server
  - open interactive sftp shell: `sftp remoteuser@remotehost` (then use `help` to list available commands)
  - fetch a remote file: `sftp remoteuser@remotehost:/home/remoteuser/remotefile`
- **scp** (Secure Copy)
  - copies files from a remote system to the local system (or vice versa)
  - NOT SEECURE anymore (use rsync instead)
- **rsync**
  - standard for secure copy
  - minimizes copied data by synchronizing only the changed portions
  - pull from remote host: `rsync [OPTION...] [USER@]HOST:SRC... [DEST]`
  - push to remote host `rsync [OPTION...] [USER@]HOST:SRC... [DEST]`
  - `rsync` options

    | Option | Description |
    |---|---|
    | -a | preserve most file characteristics (equv. to -rlptgoD) |
    | -n | dry run (only show changes that WOULD be made) |
    | -X | preserve SELinux file contexts |
    | -A | preserve ACLs |
    | -i | output a change-summary for all updates |
    | -v | verbose output |
  
## Tune System Performance
- **tuned** daemon applies tuning adjustments both statically and dynamically by using tuning profiles
  - config in */lib/tuned/PROFILENAME/tuned.conf*
  - show current-profile: `tuned-adm profile_info`
  - change profile: `tuned-adm profile balanced`
  - get profile recommendation: `tuned-adm recommend`
- **nice value**
  - determines a process' priority (higher nice value -> lower priority)
  - ranges from -20 to 20
  - privileged user can **decrease** the nice value of a process
  - unprivileged users can only **increase** the nice value
  - start process with user defined nice value: `nice -n 15 COMMAND`
  - change nice value of a process: `$ renice -n 19 PID`
  

## Manage SELinux Security
- Protect and manage server security by using SELinux.
  - managed trough semanage command and */etc/sysconfig/selinux* config file
- SELinux can be set to two enforcement modes **Enforcing** or **Permissive**
  - `getenforce`
  - `setenforce [ Enforcing | Permissive | 1 | 0 ]`
  - set persistently in */etc/selinux/config*
- SELinux can change the **fcontext** of a file
  - show file context: `ls -Z FILE`
  - change file context: `semanage fcontext [-a|-m] -t FILECONTEXT FILE(S) && restorecon -Rv FILE(S)`
  - commonly used with pirate `semanage fcontext -a -t httpd_sys_content_t '/virtual(/.*)?'`
  - list all fcontext options: `seinfo -t`
- SELinux can set **booleans**
  - show all booleans: `semanage boolean -l` or `getsebool -a`
  - search for boolean: `semanage boolean -l | grep NAME` or `getsebool NAME`
  - set boolean: `setsebool NAME [on|off]`
- SELinux can link **ports** and services that are allowed to use them
  - list all port definitions: `semanage port -l`
  - add port definition (example): `semanage port -a -t http_port_t -p tcp 81`
- **Troubleshooting**
  - logs in */var/log/audit/audit.log*
  - use `ausearch` to query audit logs


## Manage Basic Storage
- Create and manage storage devices, partitions, file systems, and swap spaces from the command line.
  - for normal file system
    - `parted *device_name* mklabel msdos`
    - `parted *device_name* mkpart primary *file-system-type* *start* *end*`
    - `echo "*device_name or UUID=..* *mountpoint* *filesystem* defaults 0 0" >> /etc/fstab`
    - `mount *device_name*`
  - for SWAP
    - `parted *device_name* mklabel msdos`
    - `parted *device_name* mkpart primary linux-swap *start* *end*`
    - `mkswap *partition_name*`
    - `echo "*device_name or UUID=..* swap swap defaults 0 0" >> /etc/fstab`
    - `swapon -a`
`
## Manage Storage Stack
- Create and manage **logical volumes** that contain file systems or swap spaces from the command line.
    - Initialize Devices/Partitions
        - if you're using partitions make sure you set them up properly with parted mkpart and udevadm settle
        - to make sure devices/partitions are unmounted check lsblk --fs
        - initialize them as PVs with `pvcreate /dev/sdb1 /dev/vdc /dev/sdc...` (you get the point)
    - Create Volume Group
        - use `vgcreate *groupname* /dev/sbd1 /dev/vdc...` to create group
        - use `vgdisplay` to show group stats
        - use `vgextend *groupname* /dev/vdb3` to add further PVs to the VG
    - Create Logical Volume and Filesystem
        - use `lvcreate -n *volume-name* -L *volume-size* *groupname*` to create a new LG in the group
        - use `lvextend/lvdisplay` to extend the volume or show stats
        - to create a filesystem on the LV use `mkfs -t xfs /dev/*groupname*/*volume-name*`
        - mount filesystem with `echo '/dev/*groupname*/*voulme-name* *mountpoint* xfs defaults 0 0' >> /etc/fstab && mount *mountpoint*`
        - after growing a LV remember to grow the filesystem with commands such as `xfs_growfs *mountpoint*`
- Describe logical volume manager components and concepts, and implement LVM storage and display LVM component information.
- Analyze the multiple storage components that make up the layers of the storage stack.

## Access Network-Attached Storage
- Access network-attached storage with the NFS protocol.
    - `echo "servera:/share/stuff /mnt/stuff nfs defaults 0 0" >> /etc/fstab`
    - `mount -a`
- Describe the benefits of using the automounter, and automount NFS exports by using direct and indirect maps
    - create master map file to /etc/auto.master.d. This file identifies the base directory for mount points and the mapping file to create the automounts.
        - eg. /shares /etc/auto.indirect (indirect)
        - or  /-      /etc/auto.direct   (direct)
    - create mapping file
        - eg. work      -rw,sync serverb:/shares/work  (indirect)
        - or  /mnt/docs -rw,sync serverb:/shares/docs  (direct)
    - wildcards to share all subdirectories and use the same name on client system (only with indirect mapping)
        - eg. * -rw,sync serverb:/shares/&
    - after making changes restart autofs with $ systemctl restart autofs.service
- Provide a shared folder on the network
  - ensure nfs-server is installed: `dnf install nfs-utils`
  - create entry in */etc/exports* (see exports(5) for details): `echo "/srv/folder   *(ro,sync,no_subtree_check)" >> /etc/exports`
  - reload nfs-server: `systemctl reload nfs-server || systemctl start nfs-server`
  - 

## Control the Boot Process
- Manage the boot process to control offered services and to troubleshoot and repair problems.
    - Enter emergency mode by appending systemd.unit=emergency.target to linux kernel line in boot parameters
    - login as root and remount filesystem in r/w mode $  mount -o remount,rw / && mount -a
    - often there can be issues in the /etc/fstab file $ vim /etc/fstab && systemctl daemon-reload
- Describe the Red Hat Enterprise Linux boot process, set the default target when booting, and boot a system to a non-default target.
    - edit boot params and set systemd.unit=multi-user.target for example
    - once booted the default can be changed with $ systemctl set-default
- Log in to a system and change the root password when the current root password is lost.
- Manually repair file-system configuration or corruption issues that stop the boot process.

## Manage Network Security
- Control network connections to services with the system firewall and SELinux rules.
- Accept or reject network connections to system services with firewalld rules.
- Verify that network ports have the correct SELinux type for services to bind to them.
- **firwalld** = firewall daemon
  - mapping from zone to service/port/interface...
    - zone defines the behaviour of the firewall eg. drop packets, block requests, allow access etc.
  - configure firewalld through `firewall-cmd`
  - `firewall-cmd --reload` : Reload firewall rules and keep state information
  - `firewall-cmd blablabla --permanent` : set options permanently
  - `firewall-cmd --get-default-zone`
  - `firewall-cmd --set-default-zone ZONE` : 
  - `firewall-cmd --list-all` : show info for currently active zone(s)
  - `firewall-cmd --new-zone=ZONE` : add new zone
  - `firewall-cmd  --add-[service|source|port|interface]` : add service to current zone
- **SELinux** Port Labeling
  - SELinux assigns a context/label to all files, devices, directories, ports... (here generalized as units)
  - a label/context defines the access control behaviour of a unit
  - also manages booleans like *ssh_keysign* (allow ssh to keysign)
  - enforcement status
    - enforcing = SELinux security policy is enforced.
    - permissive = SELinux security policy is not enforced but logs the warnings (i.e. the action is allowed to proceed).
  - managed through `semanage [fcontext|port|booleans|module...] [arguments]`


## Containers
*coming soon*


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
| /etc/login.defs| default values fort `useradd`          | login.defs(5) | root  | yes      |

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
| /etc/exports | files exported as nfs shares |

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

### Binaries
| Directory | Description |
|---|---|
|/bin | essential system binaries |
|/usr/bin | non-essential system binaries |
|/usr/sbin | non-essential system binaries that require root privileges |