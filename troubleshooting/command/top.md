



top -o %CPU # Sort by CPU usage
top -o %MEM # Sort by memory usage



```
top - 02:01:42 up 1 min,  1 user,  load average: 0.15, 0.05, 0.02
Tasks: 130 total,   2 running, 128 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.2 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.2 hi,  0.0 si,  0.0 st
MiB Mem :   1778.4 total,   1431.4 free,    144.8 used,    202.3 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   1465.6 avail Mem 

```



 **top 命令输出**中每个指标的含义：

------

### 第一行（系统运行时间和负载）

```
top - 02:01:42 up 1 min,  1 user,  load average: 0.15, 0.05, 0.02
```

- **02:01:42** → 当前系统时间
- **up 1 min** → 系统已运行时间（这里是 1 分钟）
- **1 user** → 当前有 1 个已登录用户
- **load average: 0.15, 0.05, 0.02** → 系统 1 分钟、5 分钟、15 分钟的平均负载。
  - **负载**指正在运行队列（CPU 可执行进程 + 等待 IO 的进程）的平均数量。
  - 一般经验：负载数值 ≈ CPU 核心数时，说明刚好满负荷。如果 1 核 CPU 上 load > 1，就意味着有进程在排队等待。

------

### 第二行（任务/进程状态）

```
Tasks: 130 total,   2 running, 128 sleeping,   0 stopped,   0 zombie
```

- **130 total** → 系统中共有 130 个进程
- **2 running** → 当前正在运行（占用 CPU）的进程数
- **128 sleeping** → 处于休眠（等待事件或 IO）的进程数
- **0 stopped** → 被暂停的进程数
- **0 zombie** → 僵尸进程（已退出但父进程未回收资源）的数量

------

### 第三行（CPU 使用率）

```
%Cpu(s):  0.2 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.2 hi,  0.0 si,  0.0 st
```

各字段含义：

- **us (user)** 0.2% → 用户空间占用 CPU 时间（应用程序运行消耗）
- **sy (system)** 0.0% → 内核空间占用 CPU 时间（系统调用、内核进程）
- **ni (nice)** 0.0% → 调整过 nice 优先级的进程占用 CPU 时间
- **id (idle)** 99.7% → 空闲 CPU 时间
- **wa (iowait)** 0.0% → 等待 IO 完成的 CPU 时间
- **hi (hardware irq)** 0.2% → 硬件中断占用时间
- **si (software irq)** 0.0% → 软件中断占用时间
- **st (steal)** 0.0% → 虚拟化环境中，被 hypervisor “偷走”的 CPU 时间

👉 本例中 CPU 几乎是空闲状态（99.7% 空闲）。

------

### 第四行（内存使用情况）

```
MiB Mem :   1778.4 total,   1431.4 free,    144.8 used,    202.3 buff/cache
```

- **total 1778.4 MiB** → 物理内存总量
- **free 1431.4 MiB** → 当前完全空闲的内存
- **used 144.8 MiB** → 正在使用的内存
- **buff/cache 202.3 MiB** → 被 Linux 用作缓存（buffer + page cache）的内存，可随时回收

⚠️ 注意：Linux 把大量内存用于缓存文件 IO，这部分虽然显示“已用”，但其实可以快速释放。

------

### 第五行（Swap 使用情况）

```
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   1465.6 avail Mem
```

- **total 2048.0 MiB** → 交换分区总大小
- **free 2048.0 MiB** → 空闲 swap
- **used 0.0 MiB** → 已使用 swap（这里没有用到 swap）
- **avail Mem 1465.6 MiB** → 在不使用 swap 的情况下，可供应用程序使用的内存（free + 可回收的缓存）





看一下cpu sys较高的例子：

cpu : sys: 76.9 sy

```
top - 02:34:02 up 33 min,  1 user,  load average: 4.50, 1.85, 0.70
Tasks: 124 total,   4 running, 120 sleeping,   0 stopped,   0 zombie
%Cpu(s):  3.8 us, 76.9 sy,  0.0 ni,  0.7 id,  0.0 wa,  0.7 hi, 18.0 si,  0.0 st
MiB Mem :   1778.4 total,   1010.4 free,    156.0 used,    612.1 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   1450.8 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND  
   4813 root      20   0   64344    452      0 S  50.3   0.0   1:03.43 stress-+ 
   4812 root      20   0   64344    452      0 R  49.3   0.0   1:03.30 stress-+ 
   4815 root      20   0   64340   2488      0 R  31.1   0.1   0:41.13 stress-+ 
   4814 root      20   0   64340   2488      0 S  30.5   0.1   0:41.16 stress-+
```



cpu:  sy 95-96, system: cs 6197 都高，上下文切换高

```
vmstat  1  5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 4  0      0 1036028   3144 623672    0    0    97    43  482  921  1 13 86  0  0
 7  0      0 1035316   3144 623672    0    0     0     0 2293 6197  3 96  1  0  0
 5  0      0 1035252   3144 623672    0    0     0     0 2185 6112  4 95  0  0  0


```



定位问题：

 查看进程，上下文切换cswch

```
[root@localhost tmp]# pidstat -wt -p 4815 1
Linux 4.18.0-553.el8_10.x86_64 (localhost.localdomain) 	08/27/2025 	_x86_64_	(2 CPU)

02:38:44 AM   UID      TGID       TID   cswch/s nvcswch/s  Command
02:38:45 AM     0      4815         -   1413.00     10.00  stress-ng-sock
02:38:45 AM     0         -      4815   1413.00     10.00  |__stress-ng-sock

02:38:45 AM   UID      TGID       TID   cswch/s nvcswch/s  Command
02:38:46 AM     0      4815         -   2883.00     13.00  stress-ng-sock
02:38:46 AM     0         -      4815   2883.00     13.00  |__stress-ng-sock
```

查内核函数热点

用 perf 是最直接的：

```

perf top -p 4815

Samples: 9K of event 'cycles', 4000 Hz, Event count (approx.): 2352848382 lost: 
Overhead  Shared Object     Symbol                                              
  24.12%  [kernel]          [k] copy_user_enhanced_fast_string                  
   3.34%  [kernel]          [k] __raw_callee_save___pv_queued_spin_unlock       
   3.24%  [kernel]          [k] entry_SYSCALL_64                                
   3.14%  [kernel]          [k] pvclock_clocksource_
```



用 `strace` 抓线程：

- `-c` 表示统计各类系统调用的次数和耗时。
- 如果发现某类 syscall（如 `futex`, `epoll_wait`, `sendto`）调用量异常大，就说明问题出在这里。

```
strace -fp 4815  -c
strace: Process 5177 attached


等1分钟，^ｃ结束，会出以下结果：


strace: Process 5177 detached
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 96.90    5.520663           1   2882921           sendto
  0.97    0.055048           1     45128      5641 ioctl
  0.62    0.035409           2     16923           close
  0.34    0.019339           3      5641           openat
  0.32    0.018021           1     16923           getsockname
  0.22    0.012418           2      5641           read
  0.21    0.011816           1      8448      5641 accept4
  0.11    0.006386           1      5641           getpeername
  0.11    0.006117           2      2834           accept
  0.11    0.006008           1      5641           getsockopt
  0.11    0.005998           1      5641           setsockopt
------ ----------- ----------- --------- --------- ----------------
100.00    5.697223           1   3001382     11282 total

```



**sys 高 → 程序大量消耗 CPU 在内核态**，通常是 I/O、锁、系统调用风暴。

**排查思路**：

1. `pidstat/top -H` → 确认是哪个线程。
2. `strace -c -p <pid>` → 看系统调用类型。
3. `perf top -p <pid>` → 看内核函数热点。
4. 根据结果判断是网络、磁盘还是线程调度问题。