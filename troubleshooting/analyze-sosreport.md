## 如何分析sosreport

refer to : Where can I find cluster-related information in sosreports?

 https://access.redhat.com/articles/485213



## xsos 方式分析

https://www.2daygeek.com/xsos-a-tool-to-read-sosreport-in-linux/

在linux上安装，

```
# yum install http://people.redhat.com/rsawhill/rpms/latest-rsawaroha-release.rpm

# yum install xsos
```



```
xsos -h 
Content options:
 -a, --all      show everything
 -b, --bios     show info from dmidecode
 -o, --os       show hostname, distro, SELinux, kernel info, uptime, etc
 -k, --kdump    inspect kdump configuration
 -c, --cpu      show info from /proc/cpuinfo
 -f, --intrupt  show info from /proc/interrupts
 -m, --mem      show info from /proc/meminfo
 -d, --disks    show info from /proc/partitions + dm-multipath synopsis
 -t, --mpath    show info from dm-multipath
 -l, --lspci    show info from lspci
 -e, --ethtool  show info from ethtool
 -r, --softirq  show info from /proc/net/softnet_stat
 -n, --netdev   show info from /proc/net/dev
 -g, --bonding  show info from /proc/net/bonding
 -i, --ip       show info from ip addr (BASH v4+ required)
     --net      alias for: --lspci --ethtool --softirq --netdev --bonding --ip
 -s, --sysctl   show important kernel sysctls
 -p, --ps       inspect running processes via ps

```



```
xsos -p sosreport-d01qiesbnd03-2025-07-21-soxdrdq
PS CHECK
  Total number of threads/processes: 
    2871 / 2871
  Top users of CPU & MEM: 
    USER      %CPU    %MEM   RSS 
    root      340.1%  2.1%   2.27 GiB
    srv_sub+  156.1%  0.8%   0.53 GiB
    srv_rem+  84.0%   1.8%   1.21 GiB
    srv_ben+  78.6%   0.5%   0.36 GiB
    srv_spl+  76.4%   8.1%   5.08 GiB
    srv_ppo+  56.3%   0.7%   0.44 GiB
    mdatp     4.1%    0.4%   0.27 GiB
    kafka     2.9%    2.3%   1.48 GiB
    srv_sha+  2.1%    2.5%   1.56 GiB
    srv_soc+  1.9%    22.3%  13.91 GiB
  Uninteruptible sleep threads/processes (0/0): 
    [None]
  Defunct zombie threads/processes (0/0): 
    [None]
  Top CPU-using processes: 
    USER      PID      %CPU  %MEM  VSZ-MiB  RSS-MiB  TTY    STAT  START  TIME     COMMAND  
    root      -        93.6  -     0        0        -      RNs   15:10  7:06     - 
    root      2704837  93.6  0.1   102      89       ?      -     15:10  7:06     /usr/sbin/logrotate /etc/logrotate.conf 
    srv_sub+  2718814  70.3  0.8   3418     544      ?      -     15:17  0:43     java -Xmx512m -Xms512m 
    srv_rem+  2718840  46.1  0.9   4008     623      ?      -     15:17  0:28     java -Xmx1024m -Xms1024m 
    srv_ben+  2720217  39.0  0.1   2778     68       ?      -     15:18  0:00     java -Xmx512m -Xms512m 
    srv_spl+  1682333  38.4  8.1   5336     5180     ?      -     Jul11  5567:46  splunkd --under-systemd --systemd-delegate=yes 
    srv_spl+  -        37.0  -     0        0        -      Ssl   Jul11  5365:23  - 
    srv_ppo+  2716949  31.9  0.7   3540     451      ?      -     15:17  0:27     java -Xmx512m -Xms512m 
    srv_ben+  -        26.0  -     0        0        -      Rsl   15:18  0:00     - 
    root      2718480  19.4  0.0   27       25       pts/2  -     15:17  0:13     logrotate --debug /etc/logrotate.conf 
  Top MEM-using processes: 
    USER      PID      %CPU  %MEM  VSZ-MiB  RSS-MiB  TTY    STAT  START  TIME     COMMAND  
    srv_soc+  1668716  1.3   22.3  20021    14244    ?      -     Jul11  201:14   java -XX:+UseG1GC -XX:+PrintGC 
    srv_spl+  1682333  38.4  8.1   5336     5180     ?      -     Jul11  5567:46  splunkd --under-systemd --systemd-delegate=yes 
    srv_sha+  2782114  1.9   2.5   4954     1600     ?      -     Jul16  135:56   shared-accumulator-process -Xmx1024m -Xms1024m 
    kafka     2772622  2.0   2.3   23836    1511     ?      -     Jul16  141:11   /bin/java -cp .:/opt/tools/kafka/libs/* 
    srv_ele+  1591128  0.3   2.2   4567     1465     ?      -     Jul09  58:23    electronic-enrollment-audit -Xmx1024m -Xms1024m 
    srv_mem+  2774616  0.1   1.8   5558     1149     ?      -     Jul16  12:28    member-elig-extract-v2 -Xmx2048m -Xms2048m 
    srv_ele+  1591263  0.2   1.5   4520     959      ?      -     Jul09  49:08    electronic-enrollment-subscriptionaudit -Xmx1024m -Xms1024m 
    srv_ele+  1591205  0.0   1.3   4550     865      ?      -     Jul09  17:43    electronic-enrollment-create -Xmx1024m -Xms1024m 
    srv_abc+  1591295  0.0   1.2   4493     791      ?      -     Jul09  15:51    abc-status -Xmx1024m -Xms1024m 
    srv_rem+  2718840  46.1  0.9   4008     623      ?      -     15:17  0:28     java -Xmx1024m -Xms1024m 
  Top thread-spawning processes: 
    #    USER      PID      %CPU  %MEM  VSZ-MiB  RSS-MiB  TTY    STAT  START  TIME     COMMAND 
    478  srv_sha+  2782114  1.9   2.5   4954     1600     ?      -     Jul16  135:56   shared-accumulator-process -Xmx1024m -Xms1024m 
    372  root      2282306  0.1   0.0   353      62       ?      -     Jul16  12:19    /opt/cohesity/agent/software/crux/bin/linux_agent_exec --log_dir=/var/log/cohesity --max_log_size=30 
    77   kafka     2772622  2.0   2.3   23836    1511     ?      -     Jul16  141:11   /bin/java -cp .:/opt/tools/kafka/libs/* 
    76   root      3214     0.7   0.1   971      91       ?      -     Jun24  286:52   /opt/microsoft/mdatp/sbin/wdavdaemon edr 17 
    70   srv_ele+  1591128  0.3   2.2   4567     1465     ?      -     Jul09  58:23    electronic-enrollment-audit -Xmx1024m -Xms1024m 
    68   srv_ele+  1591205  0.0   1.3   4550     865      ?      -     Jul09  17:43    electronic-enrollment-create -Xmx1024m -Xms1024m 
    64   root      1339     1.3   0.2   1230     133      ?      -     Jun24  514:23   /opt/microsoft/mdatp/sbin/wdavdaemon 
    52   srv_soc+  1668716  1.3   22.3  20021    14244    ?      -     Jul11  201:14   java -XX:+UseG1GC -XX:+PrintGC 
    51   root      1747     0.2   0.1   2444     109      ?      -     Jun24  110:14   /opt/CARKaim/bin/appprovider -mode SERVICE 
    51   mdatp     2426389  2.0   0.4   1089     275      ?      -     11:24  4:41     /opt/microsoft/mdatp/sbin/wdavdaemon unprivileged 299 
```



```
xsos -b sosreport-d01qiesbnd03-2025-07-21-soxdrdq
DMIDECODE
  BIOS:
    Vend: Phoenix Technologies LTD
    Vers: 6.00
    Date: 11/12/2020
    BIOS Rev: 4.6
    FW Rev:   0.0
  System:
    Mfr:  VMware, Inc.
    Prod: VMware Virtual Platform
    Vers: None
    Ser:  VMware-42 30 55 e6 c2 a5 a7 c7-d5 4d e4 e1 0a a7 88 f5
    UUID: e6553042-a5c2-c7a7-d54d-e4e10aa788f5
  CPU:
    4 of 4 CPU sockets populated, 1 cores/0 threads per CPU
    4 total cores, 0 total threads
    Mfr:  GenuineIntel
    Fam:  Unknown
    Freq: 2900 MHz
    Vers: Intel(R) Xeon(R) Gold 6226R CPU @ 2.90GHz
  Memory:
    Total: 65536 MiB (64 GiB)
    DIMMs: 4 of 192 populated
    MaxCapacity: 66560 MiB (65 GiB / 0.06 TiB)


```



```
xsos -m sosreport-d01qiesbnd03-2025-07-21-soxdrdq
MEMORY
  Stats graphed as percent of MemTotal:
    MemUsed    ▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊...  93.1%
    Buffers    ..................................................   0.0%
    Cached     ▊▊▊▊▊▊▊▊▊▊▊▊▊.....................................  26.9%
    HugePages  ..................................................   0.0%
    Dirty      ..................................................   0.1%
  RAM:
    62.3 GiB total ram
    58 GiB (93%) used
    41.2 GiB (66%) used excluding Buffers/Cached
    0.07 GiB (0%) dirty
  HugePages:
    No ram pre-allocated to HugePages
  LowMem/Slab/PageTables/Shmem:
    5.44 GiB (9%) of total ram used for Slab
    0.09 GiB (0%) of total ram used for PageTables
    1.22 GiB (2%) of total ram used for Shmem
  Swap:
    0 GiB (1%) used of 2 GiB total

```



```
xsos -d sosreport-d01qiesbnd03-2025-07-21-soxdrdq
STORAGE
  Whole Disks from /proc/partitions:
    3 disks, totaling 400 GiB (0.39 TiB)
    - - - - - - - - - - - - - - - - - - - - -
    Disk 	Size in GiB
    ----	-----------
    sda 	50
    sdb 	330
    sdc 	20
```



```
xsos -o sosreport-d01qiesbnd03-2025-07-21-soxdrdq
OS
  Hostname: d01qiesbnd03.independenthealth.com
  Distro:   [redhat-release] Red Hat Enterprise Linux release 9.6 (Plow)
            [os-release] Red Hat Enterprise Linux 9.6 (Plow) 9.6 (Plow)
  RHN:      (missing)
  RHSM:     hostname = d01mrhsat06.independenthealth.com
            proxy_hostname =
  YUM:      3 enabled plugins: debuginfo-install, product-id, subscription-manager
  SELinux:  disabled  (default disabled)
  Arch:     mach=x86_64  cpu=x86_64  platform=x86_64
  Kernel:
    Booted kernel:  5.14.0-570.23.1.el9_6.x86_64
    GRUB default:     
    Build version:
      Linux version 5.14.0-570.23.1.el9_6.x86_64 (mockbuild@x86-64-01.build.eng.rdu2.redhat.com) (gcc (GCC) 11.5.0 20240719 (Red Hat 
      11.5.0-5), GNU ld version 2.35.2-63.el9) #1 SMP PREEMPT_DYNAMIC Sat Jun 14 13:10:47 EDT 2025
    Booted kernel cmdline:
      BOOT_IMAGE=(hd0,msdos1)/vmlinuz-5.14.0-570.23.1.el9_6.x86_64 root=/dev/mapper/vgos-root ro 
      crashkernel=1G-4G:192M,4G-64G:256M,64G-:512M resume=/dev/mapper/vgos-swap rd.lvm.lv=vgos/root rd.lvm.lv=vgos/swap rd.lvm.lv=vgos/usr 
      rhgb quiet
    GRUB default kernel cmdline:  
      
    Taint-check: 0  (kernel untainted)
    - - - - - - - - - - - - - - - - - - -
  Sys time:                 Local time: Mon 2025-07-21 15:21:27 EDT
           Universal time: Mon 2025-07-21 19:21:27 UTC
                 RTC time: Mon 2025-07-21 19:21:27
                Time zone: America/New_York (EDT, -0400)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
  Boot time: Tue Jun 24 07:10:58 PM EDT 2025
  Time Zone: America/New_York
  Uptime:    26 days, 20:06,  3 users
  LoadAvg:   [4 CPU] 12.42 (310%), 9.26 (232%), 8.79 (220%)
  /proc/stat:
    procs_running: 15   procs_blocked: 0    processes [Since boot]: 57219988
    cpu [Utilization since boot]:
      us 60%, ni 16%, sys 13%, idle 11%, iowait 0%, irq 1%, sftirq 0%, steal 0%

```



```
 xsos -e sosreport-d01qiesbnd03-2025-07-21-soxdrdq
ETHTOOL
  Interface Status:
    ens192  0000:0b:00.0  link=up 10000Mb/s full (autoneg=N)  rx ring 1024/4096  drv vmxnet3 v1.9.0.0-k-NAPI / fw UNKNOWN
    lo      PCI UNKNOWN   link=up                             rx ring UNKNOWN    drv UNKNOWN / fw UNKNOWN
  Interface Errors:
    [None]

root@qxu-thinkpadt14sgen2i:/home/qxu/Downloads# xsos -n sosreport-d01qiesbnd03-2025-07-21-soxdrdq
NETDEV
  Interface  RxMiBytes  RxPackets  RxErrs  RxDrop      RxFifo  RxComp  RxFrame  RxMultCast
  =========  =========  =========  ======  ======      ======  ======  =======  ==========
  ens192     603716     1457585 k  0       81109 (0%)  0       0       0        118928 (0%)
  - - - - - - - - - - - - - - - - -
  Interface  TxMiBytes  TxPackets  TxErrs  TxDrop      TxFifo  TxComp  TxColls  TxCarrier 
  =========  =========  =========  ======  ======      ======  ======  =======  ==========
  ens192     876589     1087702 k  0       0           0       0       0        0 

SOCKSTAT
  sockets: used 900
  TCP: inuse 42 orphan 0 tw 75 alloc 363 mem 79
  UDP: inuse 5 mem 254
  UDPLITE: inuse 0
  RAW: inuse 0
  FRAG: inuse 0 memory 0

egrep: warning: egrep is obsolescent; using grep -E
root@qxu-thinkpadt14sgen2i:/home/qxu/Downloads# xsos -r sosreport-d01qiesbnd03-2025-07-21-soxdrdq
SOFTIRQ
  Backlog max is sufficient (Current value: net.core.netdev_max_backlog = 1000)
  Budget is not sufficient, consider reviewing budget tunable. (Current value: net.core.netdev_budget = 300)
  (see https://access.redhat.com/solutions/1241943)

```



```
xsos -i sosreport-d01qiesbnd03-2025-07-21-soxdrdq
IP4
  Interface  Master IF  MAC Address        MTU     State  IPv4 Address
  =========  =========  =================  ======  =====  ==================
  lo         -          -                  65536          
0                                                         
0            up         127.0.0.1/8                       
  ens192     -          00:50:56:b0:19:82  1500           
60                                                        
9190         up         10.11.145.212/22                  

IP6
  Interface  Master IF  MAC Address                  MTU     State  IPv6 Address                                 Scope
  =========  =========  =================            ======  =====  ===========================================  =====
  lo         -          -                            65536                                                       
0                                                                                                                
0            up         ::1/128                      host                                                        
  ens192     -          00:50:56:b0:19:82            1500                                                        
60                                                                                                               
9190         up         fe80::250:56ff:feb0:1982/64  link      
```



```
xsos -s sosreport-d01qiesbnd03-2025-07-21-soxdrdq
SYSCTLS
  kernel.
    hostname =  "d01qiesbnd03.independenthealth.com"
    osrelease =  "5.14.0-570.23.1.el9_6.x86_64"
    tainted =  "0"  (kernel untainted)
    random.boot_id =  "4d9a5fa8-d396-437b-a52b-f578593418f6"
    random.entropy_avail [bits] =  "256"
    hung_task_panic [bool] =  "0"
    hung_task_timeout_secs =  "120"  (secs task must be D-state to trigger)
    hung_task_warnings [num_warnings] =  "10"  (will report 10 [more] warnings)
    msgmax [bytes] =  "8192"
    msgmnb [bytes] =  "16384"
    msgmni [msg queues] =  "32000"
    panic [secs] =  "0"  (no autoreboot on panic)
    panic_on_oops [bool] =  "1"
    nmi_watchdog [bool] =  "0"
    panic_on_io_nmi [bool] =  "0"
    panic_on_unrecovered_nmi [bool] =  "0"
    unknown_nmi_panic [bool] =  "0"
    panic_on_stackoverflow [bool] =  {sysctl not present}
    softlockup_panic [bool] =  "0"
    softlockup_thresh [secs] =  {sysctl not present}
    pid_max =  "4194304"
    threads-max =  "509966"
    sem [array] =  "32000  1024000000  500  32000"
      SEMMSL (max semaphores per array) =  32000
      SEMMNS (max sems system-wide)     =  1024000000
      SEMOPM (max ops per semop call)   =  500
      SEMMNI (max number of sem arrays) =  32000
    shmall [4-KiB pages] =  "18446744073692774399"  (70368744177600.0 GiB max total shared memory)
    shmmax [bytes] =  "18446744073692774399"  (17179869183.98 GiB max segment size)
    shmmni [segments] =  "4096"  (max number of segs)
    sysrq [bitmask] =  "16"  (see proc man page)
    sched_min_granularity_ns [nanosecs] =  {sysctl not present}
    sched_latency_ns [nanosecs] =  {sysctl not present}
  fs.
    file-max [fds] =  "131072"  (system-wide limit for num open files [file descriptors])
    nr_open [fds] =  "1073741816"  (per-process limit for num open files [see also RLIMIT_NOFILE])
    file-nr [fds] =  "8128  0  131072"  (num allocated fds, N/A, num free fds)
    inode-nr [inodes] =  "2703357  547"  (nr_inodes allocated, nr_free_inodes)
  net.
    core.busy_read [microsec] =  "0"  (off)
    core.busy_poll [microsec] =  "0"  (off)
    core.netdev_budget [packets] =  "300"
    core.netdev_max_backlog [packets] =  "1000"
    core.rmem_default [bytes] =  "212992"  (208 KiB)
    core.wmem_default [bytes] =  "212992"  (208 KiB)
    core.rmem_max [bytes] =  "212992"  (208 KiB)
    core.wmem_max [bytes] =  "212992"  (208 KiB)
    ipv4.icmp_echo_ignore_all [bool] =  "0"
    ipv4.ip_forward [bool] =  "0"
    ipv4.ip_local_port_range [ports] =  "32768  60999"  (defines ephemeral port range used by TCP/UDP)
    ipv4.ip_local_reserved_ports [ports] =  ""  (comma-separated ports/ranges to exclude from automatic port assignments)
    ipv4.tcp_max_orphans [sockets] =  "262144"  (16384 MiB @ max 64 KiB per orphan)
    ipv4.tcp_mem [4-KiB pages] =  "762627  1016836  1525254"  (2.91 GiB, 3.88 GiB, 5.82 GiB)
    ipv4.udp_mem [4-KiB pages] =  "1525254  2033673  3050508"  (5.82 GiB, 7.76 GiB, 11.64 GiB)
    ipv4.tcp_window_scaling [bool] =  "1"
    ipv4.tcp_rmem [bytes] =  "4096  131072  6291456"  (4 KiB, 128 KiB, 6144 KiB)
    ipv4.tcp_wmem [bytes] =  "4096  16384  4194304"  (4 KiB, 16 KiB, 4096 KiB)
    ipv4.udp_rmem_min [bytes] =  "4096"  (4 KiB)
    ipv4.udp_wmem_min [bytes] =  "4096"  (4 KiB)
    ipv4.tcp_sack [bool] =  "1"
    ipv4.tcp_timestamps [bool] =  "1"
    ipv4.tcp_fastopen [bitmap] =  "1"  (enable send; see ip-sysctl.txt)
    ipv4.ipfrag_high_thresh [bytes] =  "4194304"  (4096 KiB)
    ipv4.ipfrag_low_thresh [bytes] =  "3145728"  (3072 KiB)
    ipv6.ip6frag_high_thresh [bytes] =  "4194304"  (4096 KiB)
    ipv6.ip6frag_low_thresh [bytes] =  "3145728"  (3072 KiB)
  vm.
    dirty_ratio =  "20"  (% of total system memory)
    dirty_bytes =  "0"  (disabled -- check dirty_ratio)
    dirty_background_ratio =  "10"  (% of total system memory)
    dirty_background_bytes =  "0"  (disabled -- check dirty_background_ratio)
    dirty_expire_centisecs =  "3000"
    dirty_writeback_centisecs =  "500"
    max_map_count =  "262144"
    min_free_kbytes =  "67584"
    nr_hugepages [2-MiB pages] =  "0"
    nr_overcommit_hugepages [2-MiB pages] =  "0"
    overcommit_memory [0-2] =  "0"  (heuristic overcommit)
    overcommit_ratio =  "50"
    oom_kill_allocating_task [bool] =  "0"  (scan tasklist)
    panic_on_oom [0-2] =  "0"  (no panic)
    swappiness [0-100] =  "60"
    vfs_cache_pressure [0-100] =  "100"
    zone_reclaim_mode [bitmask] =  "0"  (zone reclaim disabled)
```



## 日志文件检查

#### reboot,shutdown 记录

```
grep -E 'reboot|shutdown' last
reboot   system boot  4.18.0-553.el8_1 Tue Aug  5 21:18   still running
reboot   system boot  4.18.0-553.el8_1 Tue Aug  5 21:17 - 21:17  (00:00)


grep rebooting var/log/messages
Aug  5 08:15:45 localhost systemd-logind[677]: System is rebooting (hypervisor initiated shutdown).
Aug  5 08:19:34 localhost systemd-logind[675]: System is rebooting (hypervisor initiated shutdown)


grep terminating. var/log/secure 
Aug  4 20:59:40 localhost sshd[1057]: Received signal 15; terminating.
Aug  5 08:13:50 localhost sshd[695]: Received signal 15; terminating.

```

##### 示例，

　以这个记录为例，如果在记录中发现关机时间为：：May  8 21:01 ，就要在**var/log/secure ，var/log/messages 找这个时间之前的日志**，看什么原因引发的关机

```
reboot   system boot  4.18.0-553.46.1. Fri May  9 14:18   still running
shutdown system down  4.18.0-553.46.1. Thu May  8 21:01 - 14:18  (17:17)
```



查看相关可能的原因，

```
 grep -i -e "out of memory\|oom-killer\|soft lockup\|Unable to allocate memory\|blocked for more than 120 seconds\|abrt-hook-ccpp" var/log/messages* | wc -l
```



```
var/log/messages 
...
May  8 20:56:26 ns-rhl-idma0001 systemd-logind[1298]: Removed session 3457.
May  8 20:56:26 ns-rhl-idma0001 systemd-logind[1298]: Removed session 3453.
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: Stopping Disk Manager...
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: Stopping Samba SMB Daemon...
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: session-2809.scope: Killing process 775809 (tmux: server) with signal SIGTERM.
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: session-2809.scope: Killing process 775810 (bash) with signal SIGTERM.
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: session-2809.scope: Killing process 775844 (sudo) with signal SIGTERM.

var/log/secure
...
May  8 21:00:03 ns-rhl-idma0001 sssd_be[853860]: GSSAPI client step 1
May  8 21:00:03 ns-rhl-idma0001 sssd_be[853860]: GSSAPI client step 2
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: Stopping Disk Manager...
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: Stopping Samba SMB Daemon...
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: session-2809.scope: Killing process 775809 (tmux: server) with signal SIGTERM.
May  8 21:01:07 ns-rhl-idma0001 systemd[1]: session-2809.scope: Killing process 775810 (bash) with signal SIGTERM.
```



***如果sosreport 的数据找不到引起reboot的问题，是因为收集数据时，机器已经重启了，或者引发此问题的应用并未运行，这时候就要分析vmcore***

检查是否有vmcore产生，如果是 core dump引起的重启，可以进行vmcore的分析

```
tree var/crash/
```



find boot log fro:

```
var/log/audit/audit.log

ausearch -if var/log/audit/audit.log|grep -B1 -i "restart\|reboot\|shutdown"

```



find boot log from journalctl

有可能在 var/log/message中没找到启动日志，可以在journalctl中查询

```

grep 'BOOT_IMAGE' ./sos_commands/logs/journalctl_--no-pager|more
May 08 20:24:05 CMSSPLT1 kernel: Command line: BOOT_IMAGE=(hd0,gpt2)/vmlinuz-4.18.0-553.50.1.el8_10.x86_64 root=/dev/mapper/rhel-root ro crashkernel=auto resu
me=UUID=3fcec804-45e7-463f-b943-45497673f259 rd.lvm.lv=rhel/root rd.lvm.lv=rhel/usr rhgb quiet hugepages=8192 isolcpus=2-32,34 audit_backlog_limit=10000 audit
=1 ipv6.disable=1

```



#### 第三方Modules

```
cat proc/modules | grep -i "(" -c
```



#### 启动失败的服务

```
cat sos_commands/systemd/systemctl_list-units_--failed

0 loaded units listed. Pass --all to see loaded but inactive units, too.
To show all installed unit files use 'systemctl list-unit-files'.

```

#### 检查 OS error

```
grep -i -e "out of memory\|I/O error\|oom-killer\|soft lockup\|page allocation failure\|blocked for more than 120 seconds\|segfault\|stalls\|abrt-hook-ccpp" var/log/messages* | grep -i 'Apr  9' | wc -l
```



分析sar

为什么 sosreport 里没有 `sar` 日志？ https://access.redhat.com/solutions/1418303

```
sosreport -k sar.all_sar=on
```

1. **没有安装 sysstat**

   - `sar` 属于 `sysstat` 软件包。
   - 如果系统没装 `sysstat`，自然不会有 `/var/log/sa/saXX` 这些文件。

2. **没有启用数据收集**

   - 就算安装了 `sysstat`，默认 **RHEL9/8** 中 **数据收集是关闭的**。

   - 配置文件：`/etc/sysconfig/sysstat`

     ```
     ENABLED="false"   # 默认关闭
     ```

```
o CPU utilization:
  ---------------

Linux 4.18.0-553.40.1.el8_10.x86_64 (t0q21w)    05/08/2025      _x86_64_        (32 CPU)

12:00:01 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
12:40:01 PM     all      0.09      0.00      0.13      0.01      0.00     99.77
12:50:01 PM     all      0.11      0.02      0.14      0.01      0.00     99.72
01:00:01 PM     all      0.12      0.02      0.15      0.01      0.00     99.70
01:10:01 PM     all      0.10      0.00      0.13      0.01      0.00     99.77
01:20:01 PM     all      0.12      0.03      0.16      0.01      0.00     99.70


 Memory Data:
  ------------

12:00:01 AM kbmemfree   kbavail kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
12:40:01 PM 778705912 782610864   4496312      0.57     51284   7614120   3363412      0.41   2527748   5839312         8
12:50:01 PM 778694956 782610504   4494812      0.57     51284   7624364   3373936      0.41   2527836   5849728        48
01:00:01 PM 778679504 782605524   4498384      0.57     51284   7634572   3410428      0.41   2527904   5859704         4
01:10:01 PM 778663752 782602528   4499096      0.57     51284   7646836   3439424      0.42   2528008   5874184         8
01:20:01 PM 778650764 782599988   4500508      0.57     51284   7657016   3460216      0.42   2528192   5884188        12

Load average:
  ------------

Linux 4.18.0-553.40.1.el8_10.x86_64 (t0q21w)    05/08/2025      _x86_64_        (32 CPU)

12:00:01 AM   runq-sz  plist-sz   ldavg-1   ldavg-5  ldavg-15   blocked
12:30:01 PM         2      1239      2.14      2.06      2.01         0
12:40:01 PM         2      1225      2.08      2.08      2.03         0
12:50:01 PM         5      1227      2.20      2.25      2.12         0
01:00:01 PM         3      1233      2.14      2.16      2.11         0
01:10:01 PM         1      1224      2.22      2.18      2.12         0
01:20:01 PM         2      1227      2.02      2.12      2.11         0

```



##### hang/hung 分析，如果有类似问题 blocked ，一般可能会有i/o较高问题，可同步检查sar的io数据

```
$ grep -i 'blocked for more than' var/log/messages | tail -n5

Mar 24 06:35:32 sccppblrdbo104 kernel: INFO: task sqlplus:1021732 blocked for more than 120 seconds.
Mar 24 06:35:32 sccppblrdbo104 kernel: INFO: task lsof:1022694 blocked for more than 120 seconds.
Mar 24 06:37:34 sccppblrdbo104 kernel: INFO: task ds_am:28286 blocked for more than 120 seconds.
Mar 24 06:37:34 sccppblrdbo104 kernel: INFO: task pool-ds_am:28291 blocked for more than 120 seconds.
Mar 24 06:37:35 sccppblrdbo104 kernel: INFO: task sqlplus:1021732 blocked for more than 120 seconds.
```



在进程中找到内存高

```
awk '{m[$11]+=$6}END{for(item in m){printf "%s %s MiB\n",item,m[item]/1024}}' ps | sort -nrk2 | head | column -t
```





##### 升级后，查看安装的kernel和依赖替换问题引起的不能启动

```
cat installed-rpms |grep kernel-core
```



```
 cat sos_commands/rpm/package-data | grep -i "ksplice\|kernel" | grep -v "Red Hat, Inc." | cut -b1-120 | sort

glibc-2.17-323.0.1.ksplice1.el7_9.i686	Thu Mar 25 18:19:11 2021	1616710751	Oracle America	ksplice-build2.us.oracle.com	8
glibc-2.17-323.0.1.ksplice1.el7_9.x86_64	Thu Mar 25 18:10:07 2021	1616710207	Oracle America	ksplice-build2.us.oracle.com
glibc-common-2.17-323.0.1.ksplice1.el7_9.x86_64	Thu Mar 25 18:10:24 2021	1616710224	Oracle America	ksplice-build2.us.ora
glibc-devel-2.17-323.0.1.ksplice1.el7_9.x86_64	Thu Mar 25 18:17:22 2021	1616710642	Oracle America	ksplice-build2.us.orac
glibc-headers-2.17-323.0.1.ksplice1.el7_9.x86_64	Thu Mar 25 18:13:20 2021	1616710400	Oracle America	ksplice-build2.us.or
kernel-lt-4.4.171-1.el7.elrepo.x86_64	Fri Jan 18 11:21:46 2019	1547828506	The ELRepo Project (http://elrepo.org)	Build64
ksplice-helper-1.0.48-1.el7.x86_64	Wed Dec 16 17:54:08 2020	1608159248	Oracle America	ksplice-build4.us.oracle.com	89011
openssl-1.0.2k-21.ksplice2.el7_9.x86_64	Sun May 23 13:03:03 2021	1621789383	Oracle America	ksplice-build2.us.oracle.com	
openssl-libs-1.0.2k-21.ksplice2.el7_9.x86_64	Sun May 23 13:00:27 2021	1621789227	Oracle America	ksplice-build2.us.oracle

---

$ sos_commands/rpm/package-data | grep -i "ksplice\|kernel" | grep -v "Red Hat, Inc." | cut -b1-120 | sort

glibc-2.17-323.0.1.ksplice1.el7_9.i686	Thu Mar 25 18:19:39 2021	1616710779	Oracle America	ksplice-build2.us.oracle.com	8
glibc-2.17-323.0.1.ksplice1.el7_9.x86_64	Thu Mar 25 18:10:23 2021	1616710223	Oracle America	ksplice-build2.us.oracle.com
glibc-common-2.17-323.0.1.ksplice1.el7_9.x86_64	Thu Mar 25 18:10:40 2021	1616710240	Oracle America	ksplice-build2.us.ora
glibc-devel-2.17-323.0.1.ksplice1.el7_9.x86_64	Thu Mar 25 18:17:44 2021	1616710664	Oracle America	ksplice-build2.us.orac
```

