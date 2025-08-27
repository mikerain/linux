在 Linux 上 **swap 使用较高** 通常意味着物理内存不足或者系统调度策略导致内存被换出到 swap。常见原因有：

### 可能原因

1. **物理内存不足**
   - 系统进程实际需要的内存超过了 RAM 容量，内核被迫把部分内存页换出到 swap。
2. **内存分配过度 (overcommit)**
   - 应用分配了大量虚拟内存 (比如 Java、Python、数据库)，但未实际使用，内核还是保留了 swap 做安全保障。
3. **某些进程内存泄漏**
   - 某个应用长期运行，内存占用不断增长，挤占了物理内存，导致 swap 使用增加。
4. **内核 swappiness 参数过高**
   - `/proc/sys/vm/swappiness` 决定了内核是否倾向于把不活跃的内存页换到 swap。默认值通常是 60，可能会让 swap 提前使用。

------

### 排查 swap 使用的命令

#### 1. 查看总体 swap 使用情况

```
free -h
```

或

```
swapon --show
```

如何以上为空，代表没有配置swap

#### 2. 找出哪个进程占用 swap 最多

```
smem -rs swap
```

（需要安装 `smem`，输出会按 swap 使用量排序）

另一种不用 `smem` 的方式：

```
for pid in $(ls /proc | grep -E '^[0-9]+$'); do 
  if [ -r /proc/$pid/status ]; then 
    awk '/VmSwap|Name/ {printf "%-20s %s\n", $2, $3}' /proc/$pid/status 
  fi 
done | sort -k2 -n -r | head
```

这个会列出进程名和使用的 swap 大小。

#### 3. 查看哪个文件或设备是 swap

```
cat /proc/swaps
```

#### 4. 查看 swappiness 参数（决定 swap 使用积极性）

```
cat /proc/sys/vm/swappiness
```

值越大越容易用 swap（0 表示尽量不用，100 表示非常积极）。

------

👉 一般排查步骤是：

1. 先用 `free -h` 看 swap 使用是否确实很高；
2. 再用 `smem -rs swap` 或上面脚本看哪些进程用 swap；
3. 结合 `top` / `htop` 看内存占用；
4. 调整 swappiness 或优化内存配置（加内存 / 优化应用）。



`sar` 可以监控 **swap 空间的使用量** 以及 **swap in/out 的速率**。

#### 常见与 swap 相关的参数：

- **`sar -S`** → 显示 swap 空间使用情况（多少已用、多少可用）。

  ```
  sar -S 1 5
  ```

  每 1 秒刷新一次，共 5 次。

- **`sar -W`** → 显示 swap in/out 的速率（即每秒从磁盘换入/换出的页面数）。

  ```
  sar -W 1 5
  ```

  - `pswpin/s`：每秒换入（page swap in）
  - `pswpout/s`：每秒换出（page swap out）

这两个指标组合起来就能看出：

- **swap 已使用多少**（`-S`）
- **swap 是否频繁被使用**（`-W`，频繁说明内存压力大）。





`vmstat` **可以查看 swap 的使用情况**，但显示的方式和 `free` 或 `swapon -s` 有所不同。

------

### 1. `vmstat` 输出中与 swap 相关的字段

执行：

```
vmstat 1 5
```

常见输出：

```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 123456  65432 987654    0    0     0     1  100  200  1  1 98  0  0
```

解释：

- **swpd**：当前已使用的 swap 空间大小（单位：KB）。
- **si**（swap in）：从磁盘换入到内存的数据量（KB/s）。
- **so**（swap out）：从内存换出到磁盘的数据量（KB/s）。

如果 **swpd 较大**，但 **si/so 很小**，说明 swap 占用多，但没有频繁换入/换出，可能是内核把不常用的内存页丢到了 swap 里。
 如果 **si/so 持续较大**，说明正在频繁发生 swap，性能会受严重影响。

------

### 2. 举例

```
vmstat 2
```

每 2 秒打印一次内存和 swap 使用情况。

- 若看到：

  ```
  swpd   si   so
  204800 0    0
  ```

  → 有 200MB swap 被占用，但当前没在交换，属于“静态占用”。

- 若看到：

  ```
  swpd   si   so
  409600 120  256
  ```

  → swap 使用中，且频繁在磁盘和内存之间换页，说明内存压力大。

------

### 3. 其他查看 swap 使用的命令

- `free -m`：查看 swap 总量和已用量。
- `swapon -s` 或 `cat /proc/swaps`：查看 swap 分区/文件。
- `sar -W 1`：查看每秒 swap in/out。
- `top` / `htop`：实时查看 swap 使用情况。



添加或配置新的swap

```
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile swap swap defaults 0 0' | sudo tee -a /etc/fstab
```



### 模拟一个swap的测试，当前环境配置为2C/2G

```
stress-ng --vm 2 --vm-bytes 2G --vm-keep --timeout 600s &
```



```
root@localhost /]# vmstat 1 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 4  0 582484  69704      0  50956  110  103   118  2375   81   69  2  0 97  0  0
 2  0 570896  73360      0  37356 544584 533420 550524 533420 69699 50787 14 43 26 17  0
 4  0 563596  67272      0  36628 547980 540496 558840 540496 68788 50270 12 47 24 17  0
 4  0 563456  63176      0  39164 519400 518684 529132 518684 63986 50115 12 42 25 21  0
 2  0 578564  78112      0  36128 522368 536164 526204 536164 64707 51084 13 42 26 19  0
[root@localhost /]# free -m 
              total        used        free      shared  buff/cache   available
Mem:           1778        1664          63           6          50          13
Swap:          2047         568        1479

[root@localhost /]# sar -W 1
Linux 4.18.0-553.el8_10.x86_64 (localhost.localdomain) 	08/26/2025 	_x86_64_	(2 CPU)
02:09:29 AM  pswpin/s pswpout/s
02:09:30 AM 130617.00 101492.00
02:09:31 AM 141035.00  91619.00
02:09:32 AM 123738.00 104994.00
02:09:33 AM  80869.00  82518.00

```



------

## 1️⃣ vmstat 输出分析

```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 4  0 582484  69704      0  50956  110  103   118  2375   81   69  2  0 97  0  0
 2  0 570896  73360      0  37356 544584 533420 550524 533420 69699 50787 14 43 26 17  0
 ...
```

- **procs**
  - `r` = 4 → 运行队列中有 4 个进程在等待 CPU，CPU 有一定压力
  - `b` = 0 → 没有进程在等待 I/O
- **memory**
  - `swpd` = 582484 KB → 已使用 swap，大约 568 MB（和 `free -m` 对应）
  - `free` = 69704 KB → 可用物理内存只有 ~68 MB
  - `buff` = 0，`cache` = 50956 KB → 系统几乎没有可用缓存
- **swap**
  - `si` = 110 → 每秒从 swap 换入 110 KB（第一行低压状态）
  - 第二行 `si = 544584`，`so = 533420` → **大量 swap in/out**，说明内存压力极高，系统频繁在 swap 与物理内存间交换页面
- **cpu**
  - `us` 2~~14，`sy` 0~~47，`id` 97~~26，`wa` 0~~21 → CPU 部分被系统内核占用较多，并且有等待 I/O 的时间 (`wa`)，可能和 swap I/O 相关

✅ 结论：系统 **内存紧张，swap 活跃，CPU 有部分系统占用**。

------

## 2️⃣ free -m 输出分析

```
Mem:           1778        1664          63           6          50          13
Swap:          2047         568        1479
```

- **物理内存**：
  - 总量 1778 MB
  - 已用 1664 MB → **使用率超过 93%**
  - 可用内存只有 63 MB，available 13 MB → 几乎耗尽
- **swap**：
  - 总量 2047 MB
  - 已使用 568 MB → swap 已经被系统占用，说明内存不足，系统开始使用 swap
  - 可用 swap 1479 MB → 还有一定空间

✅ 结论：系统物理内存快满，已开始频繁使用 swap。

------

## 3️⃣ sar -W 输出分析

```
02:09:30 AM 130617.00 101492.00
02:09:31 AM 141035.00  91619.00
02:09:32 AM 123738.00 104994.00
02:09:33 AM  80869.00  82518.00
```

- **pswpin/s** → 每秒从 swap 换入内存的 KB
- **pswpout/s** → 每秒写入 swap 的 KB
- 数值非常大（几十万 KB/s，约 100~140 MB/s） → **系统正在进行大量 swap in/out**
- 这种情况会严重影响性能，因为 swap 在磁盘上，比内存慢得多

✅ 结论：当前系统 **内存严重不足**，大量进程在频繁交换内存页，可能导致响应延迟明显。

------

## 4️⃣ 综合结论

- 物理内存几乎耗尽（63 MB free）
- swap 已使用超过 500 MB，并且 swap in/out 非常频繁
- CPU 一部分时间在处理系统任务（大量内核活动）
- 系统可能会出现：
  - 响应变慢
  - I/O 等待增加
  - 高负载、卡顿
  - 极端情况下触发 OOM

------

## 5️⃣ 建议

1. **增加 swap**（创建 swap 文件或调整分区），缓解内存紧张。

2. **优化内存占用进程**：

   - `top` / `htop` 找出占用内存最高的进程
   - 调整或重启内存占用大的服务

3. **调整 vm.swappiness**：

   ```
   sudo sysctl -w vm.swappiness=10
   ```

   让系统尽量使用物理内存，减少 swap I/O 压力

4. **考虑升级物理内存**，当前 2GB 对多任务负载太小