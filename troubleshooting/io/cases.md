

How to find hidden disk space using bind mounts?

- https://access.redhat.com/solutions/6533871





- Too many files open
   https://access.redhat.com/solutions/2469

  

- io wait high


https://access.redhat.com/support/cases/#/case/04221602



##### How to repair filesystem in rescue environment for Red Hat Enterprise Linux?

https://access.redhat.com/solutions/9541



inode issue, No space left on device

https://access.redhat.com/solutions/1927



Extending inode count for existing ext2/ext3/ext4 file system

https://access.redhat.com/solutions/17225



文件系统readonly了，mount 为ro了

https://access.redhat.com/support/cases/#/case/04232496

使用这个命令，检查到文件系统有坏

dumpe2fs /dev/mapper/rootvg-root

```
cat 0050-command_output.txt|grep -iE "mounted|state"
Last mounted on:          /
Filesystem state:         clean with errors
```

再加上dmesg的信息

```
 dmesg -T |grep  -iE "ext4|hostbyte|read-only"
```



NFS slow issue

https://access.redhat.com/support/cases/#/case/04235274

use these 2 command to check,

### 1.

```
tcpdump -s 0 -n -i any -w /tmp/$(hostname)-$(date +"%Y-%m-%d-%H-%M-%S").pcap &
```

**用途**：抓取当前系统所有网卡上的网络包，并保存为 pcap 文件。

- `tcpdump`：网络抓包工具。
- `-s 0`：捕获整个数据包（不截断）。默认只取前 68/96 字节。
- `-n`：不解析主机名和端口名，直接显示 IP/端口号，避免 DNS 干扰。
- `-i any`：在 **所有网卡接口** 上监听，而不仅仅是某个网卡。
- `-w <文件>`：把抓到的数据写入 pcap 文件，而不是在屏幕打印。
  - 文件名是 `/tmp/<主机名>-<时间>.pcap`，方便区分和按时间存档。
- `&`：在后台运行，不阻塞终端。

👉 **结果**：生成一个 Wireshark/tshark 可分析的 `.pcap` 包，包含系统当时所有流量。

------

### 2.

```
strace -fvttTyyx -s 1024 -o /tmp/strace.out du -sh /bancs-home/*
```

**用途**：跟踪 `du -sh /bancs-home/*` 命令的系统调用，并输出详细日志。

参数解析：

- `strace`：系统调用跟踪工具，用来分析进程执行了哪些系统调用。
- `-f`：跟踪子进程。
- `-v`：详细输出，不简写参数。
- `-tt`：在每行前打印带 **时间戳** 的日志（精确到微秒）。
- `-T`：显示每个系统调用花费的时间。
- `-yy`：显示文件描述符对应的实际路径。
- `-x`：以十六进制输出字符串或缓冲区内容。
- `-s 1024`：最大打印字符串长度为 1024 字节（默认太短可能被截断）。
- `-o /tmp/strace.out`：把输出写到文件 `/tmp/strace.out`，而不是终端。
- `du -sh /bancs-home/*`：被跟踪的实际命令，用于统计 `/bancs-home` 目录下每个文件/目录大小。

👉 **结果**：你能看到 `du` 在扫描 `/bancs-home/*` 时的所有系统调用（比如 `open()`, `stat()`, `read()`），以及每次调用耗时。
 这对排查 **慢 IO / 文件系统性能问题** 很有用。

------

✅ **总结**

- **tcpdump 命令**：抓取网络流量，保存成 pcap 文件以便后续分析。
- **strace 命令**：跟踪 `du` 的系统调用过程，用于分析 IO 或性能瓶颈。



move vg lvm

https://access.redhat.com/support/cases/#/case/04236124
