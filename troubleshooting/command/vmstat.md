### 1. 基本命令

```
vmstat
```

👉 不带参数时，只打印一次系统平均状态（自启动以来的平均值），不适合实时监控。

------

### 2. 实时监控（每 1 秒刷新一次）

```
vmstat 1
```

👉 每隔 1 秒输出一次当前系统的状态，直到你按 `Ctrl+C` 停止。

示例输出：

```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 987600   3144 663268    0    0     7     4   64   56  2  0 98  0  0
```

字段含义：

- **procs**
  - `r`：运行队列中的进程数（CPU 是否繁忙）
  - `b`：等待 I/O 的进程数
- **memory**
  - `swpd`：已使用的 swap 空间
  - `free`：空闲内存（KB）
  - `buff`：用于 buffer 的内存
  - `cache`：用于 page cache 的内存
- **swap**
  - `si`：swap in，每秒从磁盘换入内存的 KB
  - `so`：swap out，每秒从内存换出到磁盘的 KB
- **io**
  - `bi`：块设备读（KB/s）
  - `bo`：块设备写（KB/s）
- **system**
  - `in`：每秒中断数
  - `cs`：每秒上下文切换次数
- **cpu**
  - `us`：用户态 CPU 使用率
  - `sy`：内核态 CPU 使用率
  - `id`：CPU 空闲率
  - `wa`：等待 I/O 所花的 CPU 时间
  - `st`：被虚拟化管理程序抢占的时间（主要在虚拟机中有用）

------

### 3. 指定输出次数

```
vmstat 2 5
```

👉 每隔 **2 秒**打印一次，总共输出 **5 次**。

------

### 4. 带单位显示（MB）

```
vmstat -S M 1
```

👉 内存、swap、I/O 等单位显示为 MB，而不是 KB。

------

### 5. 显示 CPU 扩展指标（更详细）

```
vmstat -w 1
```

👉 `-w` 会让输出更宽，方便在大内存机器上看。

------

### 

显示活跃和不活跃的内存.

[root@localhost tuned]# vmstat -a  1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free  inact active   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 972628 242524 459440    0    0     6  2725   71   58  3  0 97  0  0
 1  0      0 972508 242608 459440    0    0     0     0  103  166  0  0 100  0  0



显示磁盘统计信息.

[root@localhost tuned]# vmstat -d  1
disk- ------------reads------------ ------------writes----------- -----IO------
       total merged sectors      ms  total merged sectors      ms    cur    sec
vda     8309     19 1652657    3099 374750   1593 739784070 3582693      0    255
vdb      212      0    9392      13      0      0       0       0      0      0
sr0       70      0     439       3      0      0       0       0      0      0
vda     8309     19 1652657    3099 374750   1593 739784070 3582693      0    255
vdb      212      0    9392      13      0      0       0       0      0      0
sr0       70      0     439       3      0      0       0       0      0      0

[root@localhost tuned]# vmstat   1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 972780   3084 688592    0    0     6  2724   71   58  3  0 97  0  0
 0  0      0 972660   3084 688592    0    0     0     0  113  157  0  0 100  0  0