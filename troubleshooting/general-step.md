# 1. Basic Troubleshooting Steps

1. **Check System Logs**

```
- Logs are your first line of defense.

 sudo tail -f /var/log/syslog
 sudo journalctl -xe
 
 — For systemd-based services, use: 

 sudo systemctl status <service_name>
```

**2. Verify System Resources
**Use these commands to ensure that CPU, memory, and disk resources are adequate:

```
top # Interactive view of running processes
htop # More user-friendly version of 'top'
df -h # Check disk usage
free -m # Check memory usage
```

**3. Identify and Kill Hanging Processes**

```
ps aux | grep <service_name>
sudo kill -9 <PID>
```

# 2. Common Linux Issues and Solutions

## 2.1. Service Not Running or Crashing

\- A critical service is inactive.
**- Diagnosis:** Check the service status and logs.

```
sudo systemctl status <service_name>
sudo journalctl -u <service_name> - since "5 minutes ago"
 
- Solution: Restart the service:
 
sudo systemctl restart <service_name>
```

If the service fails to restart, inspect logs and configuration files in `/etc`.

## 2.2. Network Issues

\- Server is unreachable, or there are network delays.
**- Diagnosis:** Check the network interface and connectivity:

```
ip a
ping -c 4 google.com
 
- Solution:
 
- Restart the network interface: 

sudo ifdown <interface> && sudo ifup <interface>
```

— For DNS issues, verify `/etc/resolv.conf`.

## 2.3. Disk Space Issues

\- System reports “No space left on device.”
**- Diagnosis:** Find which directories are consuming space.

```
du -sh /var/* | sort -rh | head -10
 
- Solution:
 - Clean up logs: 

 sudo rm -f /var/log/*.old
 
 - Empty cache:
 
 sudo sync && sudo sysctl -w vm.drop_caches=3
```

## 2.4. Slow Performance

\- Applications or services are slow to respond.
\- **Diagnosis:** Use `top` or `htop` to find processes consuming excessive resources.

```
top -o %CPU # Sort by CPU usage
top -o %MEM # Sort by memory usage
 
- Solution:
 - Stop unnecessary services:
 
sudo systemctl stop <service_name>
 
 - Tune system performance using `sysctl`:
 
sudo sysctl -w vm.swappiness=10
```

## 2.5. Permission Issues

\- Users or scripts encounter “Permission denied” errors.
**- Diagnosis**: Check permissions and ownership of files:

```
ls -l <file>
 
- Solution:
 - Correct ownership: 
 sudo chown <user>:<group> <file>
 
 - Adjust permissions:
 
 sudo chmod 755 <file>
```

## 2.6. Boot Issues

\- System fails to boot or is stuck in a boot loop.
\- **Diagnosis:** Boot into rescue mode or access GRUB to analyze logs.

```
- Access GRUB:
 1. Press Esc or Shift during boot.
 2. Select the recovery mode.
 - Inspect logs:
 
 sudo journalctl -b -1 # Logs from the previous boot
 
- Solution: Reinstall the bootloader:
 
 sudo grub-install /dev/sdX
 sudo update-grub
```

# **3. Useful Commands for Linux Troubleshooting**

```
| Command | Purpose |

| dmesg | View kernel messages |
| strace | Trace system calls of a process |
| netstat / ss | Check network connections |
| lsof | List open files by processes |
| iotop | Monitor disk I/O usage |
| uptime | Check system load averages |
```

# 4. Troubleshooting Workflow Example

Let’s say a web server on your Linux machine isn’t responding. Here’s how to approach it:

```
1. Check if the service is running:
 
 sudo systemctl status nginx
 
2. Inspect the logs for any errors:
 
sudo journalctl -u nginx - since "10 minutes ago"
 
3. Verify network status:
 
 ss -tulwn | grep :80
 
4. Check for firewall rules:
 
 sudo iptables -L -n | grep 80
 
5. Restart the service if needed:
 
 sudo systemctl restart nginx
```

# 5. Troubleshooting Advanced Issues in Linux

Let’s expand to more complex scenarios, covering deeper topics like kernel issues, SELinux, SSH problems, and package management issues.

# 5.1. Kernel Issues and Crashes

Kernel panic, system crash, or freezing.

**Diagnosis:** Check the kernel logs for error messages:

```
sudo dmesg | tail -50 sudo journalctl -k -p err
```

**Check for kernel updates:**

```
sudo apt update && sudo apt upgrade linux-generic
```

**Roll back to a stable kernel:**
At the GRUB menu, select an older version of the kernel.

**Enable Kernel Crash Dumps:**

Install the crash tool:

```
sudo apt install kexec-tools crash kdump-tools sudo systemctl enable kdump
```

Reboot and analyze crash dumps stored in `/var/crash/`.

## 5.2. SSH Connectivity Issues

Unable to SSH into a remote server.

**Diagnosis:** Check the SSH service status:

```
sudo systemctl status ssh

#Test connectivity with:

telnet <server_ip> 22  # Verify if the port is open.
```

**Solution:**

**Restart the SSH service:**

```
sudo systemctl restart ssh

#Check firewall rules:

sudo ufw status sudo ufw allow 22/tcp
```

**Inspect the SSH configuration:**
Look for typos or misconfigurations in `/etc/ssh/sshd_config`.

**Fix permission issues on SSH keys:**

```
chmod 600 ~/.ssh/id_rsa chmod 700 ~/.ssh
```

## 5.3. SELinux Denials and Policy Issues

Processes fail to execute despite correct permissions, or services stop unexpectedly.

**Diagnosis:** Look for SELinux denials:

```
sudo ausearch -m AVC,USER_AVC -ts recent sudo journalctl | grep "denied"
```

**Solution:**

**Temporarily disable SELinux:**

```
sudo setenforce 0
```

**Identify the issue and apply the correct policy:**

```
sudo ausearch -c "<service>" --raw | audit2allow -M my_policy sudo semodule -i my_policy.pp
```

**Re-enable SELinux:**

```
sudo setenforce 1
```

## 5.4. Package Management Issues (APT/YUM/DNF)

Unable to install or update packages; package manager errors out.

**Diagnosis:** Check for broken dependencies or locked package managers:

```
sudo apt-get update sudo dpkg --configure -a
```

**Solution:**

**Resolve broken packages:**

```
sudo apt-get install -f
```

**Remove lock files:**

```
sudo rm /var/lib/apt/lists/lock sudo rm /var/lib/dpkg/lock-frontend
```

**Clean the package cache:**

```
sudo apt clean && sudo apt autoremove
```

## 5.5. High I/O Wait and Disk Bottlenecks

System performance slows due to disk I/O bottlenecks.

**Diagnosis:**Use `iotop` to monitor disk I/O:

```
sudo iotop -o
```

Check for high I/O wait times:

```
vmstat 1 5
```

**Solution:**

**Identify the heavy I/O process:**
Use `lsof` to find files being written to:

```
sudo lsof +D /var
```

**Limit I/O using** `**ionice**`**:**

```
sudo ionice -c3 -p <PID>
```

**Optimize disk usage:**

Use `tmpfs` for temporary files to avoid disk writes:

```
mount -t tmpfs -o size=1G tmpfs /mnt/tmp
```

## 5.6. Filesystem Corruption Issues

Files are missing, or the system throws I/O errors.

**Diagnosis:** Run `fsck` to scan for filesystem issues:

```
sudo fsck -y /dev/sdX1
```

**Solution:**

**Unmount the filesystem:**

```
sudo umount /dev/sdX1
```

**Repair the filesystem:**

```
sudo fsck -f /dev/sdX1
```

**Check the SMART status of the disk:**

```
sudo smartctl -H /dev/sdX
```

## 5.7. System Lockdown Due to Firewall Rules

After applying firewall rules, remote access becomes blocked.

**Diagnosis:** Check the current firewall rules:

```
sudo iptables -L -n -v
```

**Solution:**

**Flush all firewall rules temporarily to regain access:**

```
sudo iptables -F
```

**Allow essential ports:**

```
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

**Save new rules:**

```
sudo iptables-save > /etc/iptables/rules.v4
```

## 5.8. Time Synchronization Issues (NTP/Chrony)

Incorrect time on the system causes issues with authentication, logging, or certificates.

**Diagnosis:**

check the system clock:

```
date timedatectl status
```

## Verify NTP sync status:

```
ntpq -p  # For NTP chronyc tracking  # For Chrony
```

**Solution:**

**Restart the NTP/Chrony service:**

```
sudo systemctl restart ntp sudo systemctl restart chronyd
```

**Manually sync the time:**

```
sudo ntpdate pool.ntp.org
```