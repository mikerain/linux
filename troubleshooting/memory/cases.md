

Memory management questions

https://access.redhat.com/solutions/58689



High Memory Utilization

https://access.redhat.com/support/cases/#/case/04236401

HugePages 

shared memory





swap high

suggest to config hugpage for db service

https://access.redhat.com/support/cases/#/case/04232626



What is cache in "free -m" output and why is memory utilization high for cache?

https://access.redhat.com/solutions/67610



What information do I need to investigate in case of high load, I/O or memory consumption in RHEL?

https://access.redhat.com/solutions/24626


aaa

​	

How do I check for hugepages usage and what is using it?

https://access.redhat.com/solutions/320303



hugepages,配置后，其他应用就使用不了了，所以建议降低这个配置，除非是数据库专用的。





https://access.redhat.com/solutions/225303



#### How to check high memory utilization?

 Solution Verified - Updated August 6 2024 at 3:13 PM - 

[English ](https://access.redhat.com/solutions/225303)

## Environment

- Red Hat Enterprise Linux (RHEL) 6, 7, 8 and 9
- monitoring

## Issue

- High memory utilization observed. Average utilization is 90% to 98%
- How to check memory utilization?

## Resolution

**Memory utilization:**

From a single percentage of Memory utilization in GNOME System Monitor to the more in-depth statistics reported by the commands mentioned below, it is possible to accurately determine how much Memory is being consumed and by what. Memory include RAM and swap.

**free command**
free command to check memory usage on RHEL.

Displays a line containing the totals memory in MB:

```
# free -t -m
```

**/proc/meminfo file**
This consist virtual files that contain dynamic information about the kernel and the system.

```
# cat /proc/meminfo
```

**vmstat command**
It displays statistics of virtual memory, kernel threads, disks, system processes, I/O blocks, interrupts, CPU activity.

```
# vmstat -s
```

**top command**
top is used to check memory and CPU usage per process. It displays CPU usage, Memory usage, Swap Memory, Cache Size, Buffer Size, Process PID, User, Commands.

```
# top
```

**iostat command**
It shows input and output storage device statistics.

```
# iostat -x
```

**ps command**
The ps command show per-process memory usage in real-time.

```
# ps aux
# ps auxH
```

**sar**
The sysstat package is an excellent way to monitor performance statistics, including CPU usage.

```
# sar
# sar -A
```

**lsof command**

- This command is used to check the file opened by the process or command.
- The open files included are disk files, network sockets, pipes, devices and processes.
- This command is mainly useful when a disk cannot be unmounted and displays the error that files are being used or opened.

```
# lsof
```

**gnome-system-monitor command**
System monitor display basic system information and monitor system processes, usage of system resources, and file systems.

```
# gnome-system-monitor
```

**df command**
df command displays the information of device name, total blocks, total disk space, used disk space, available disk space and mount points on a file system.

```
# df -a   ---> all information about memory
# df -h   ---> Human readable format memory
# df -i   ---> show inodes detail
# df -T   ---> show with file system type
```

-To find out hardware information about the installed RAM

```
# dmidecode
```