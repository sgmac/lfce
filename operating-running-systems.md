# Operating running systems

# Trobleshooting system performance

- `top, vmstat, lsof, tcpdump, netstat, ss, htop, itop, iostat`

- `iotop` to see the top process using more io.

- `iftop` is going so you the connections using more network.

- `nethog` determines network usage by process.

- `iptraf` ncurses IP LAN monitor, TCP info, ICMP, stats....


# Update the kernel and ensure the system is bootable


- Any update to  **/etc/default/grub**  requires `update-grub2`

- Set order and be sure is bootable
- `grep menuentry /boot/grub.cfg`


# Updating packages 

- `apt update | search | dist-ugprade -s`

# Reports on system use (sar)

- Read from a file `sar -f /var/log/sa/sa10`

# Monitoring security

```
sgm@odroid:~$ ss -t -o
State     Recv-Q     Send-Q         Local Address:Port          Peer Address:Port      Process
ESTAB     0          0              192.168.1.162:ssh           192.168.1.62:55530      timer:(keepalive,95min,0)
ESTAB     0          0              192.168.1.162:shell         192.168.1.63:46140
ESTAB     0          0              192.168.1.162:ssh           192.168.1.62:45136      timer:(keepalive,13min,0)
```

- Install `audit` (use `aureport`)
- Configure `/etc/audit/audit.rules`
- Help `man -s5 auditd.conf`
- Use `auditctl`

```
# D deletes all previous rules
D
-b 8192
-f 1
--backlog_wait_time 0
```


# Recovery Linux System

- Go to `GRUB` add at the end of the `linux` line and add to the end `rd.break`.
- Go to `GRUB` add at the end of the `linux` line `rescue`.
- Go to `GRUB` add at the end of the `linux` line `emergency`.
- Mount `mount - o remount,rw /`
- You can set the password

# Udev

- It is a device manager for the linux kernel. Dynamically creates and removes device nodes in the `/dev` directory, hot-plugging of USB devices and network disks.

- Udev uses rules in `/etc/udev/rules.d`
- Use the tooling 

```
$ sudo udevadm control --reload-rules
$ sudo udevadm info
$ sudo udevadm test
$ sudo udevadm trigger
```

**IMPORTANT**: Chanching the name of a device, udev can only do this at boot time.


- Add a new rule

```
/etc/udev/rules.d/99-disks.rules

KERNEL=="md127", SYMLINK+="md0"
```

- Run command `udevadm trigger --type=devices --action=change` 
- Then command `udevadm control --reload-rules` 


# Configure AppArmor Policies

- **complain/lerning** are logged but not prevented.
- **enforced/confined** violations are prevented and logged.
- Every protected binary has a entry in `/etc/apparmor.d`
- `aa-complain | aa-status | aa-enforce`

# User/Group management

- Implement authentication with `sssd`
- // TODO: sssd.conf
- `nsswitch.conf` passwd|shadow| group `files sss`


# Configure Advanced PAM

- **auth** validates the identity (root/bob can loging).
- **account** allows or denies access based on account policies.
- **password** manages password change policies.
- **session** applies settings for application sessions. (env for instance)

## Control flags

- **required**  if it passes, PAM goes to the next rule of the same type. If it fails continues, but the result return is a failure.
- **requisite** strict, if it fails does not continue. (this should be the last rule, otherwise rest rules would not get process)
- **sufficient** if it passes, does not check any rule. If it fails, it continues.
- **optional** PAM ignores the result and proceeds to the next rule, either passes or fail.
- **include** Includes all rules of the same type from the designated configuration file.


