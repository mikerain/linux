



```
ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 6798
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 6798
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited

```



###### 

这个问题在 **Linux（RHEL 8）** 中很常见，通常表现为：

- **Too many open files**
- **Cannot fork: resource temporarily unavailable**
- **ulimit reached**

原因、排查和调整方法如下👇

------

## 一、常见原因

1. **进程数过多**
   - 系统允许的最大进程数 (`max user processes`) 被耗尽。
   - 可能是某些程序出现 **fork 炸弹** 或者守护进程未正确回收子进程。
2. **文件句柄（FD）过多**
   - 每个进程能打开的文件描述符有限（包括 socket、文件、管道等）。
   - 高并发服务（如 Nginx、数据库、消息队列）常见。

------

## 二、如何排查

### 1. 当前用户的限制

```
ulimit -n        # 查看最大文件描述符数（默认1024）
ulimit -u        # 查看最大进程数
ulimit -a        # 查看所有限制
```

### 2. 系统级别限制

```
cat /proc/sys/fs/file-max       # 系统所有进程可打开的最大文件数
cat /proc/sys/kernel/pid_max    # 系统最大进程数
```

### 3. 监控实际使用情况

```
lsof | wc -l                    # 当前系统打开的总文件数
lsof -u 用户名                  # 查看某个用户占用的FD
lsof -p <pid>                   # 查看某个进程打开了多少FD

ps -eLf | wc -l                 # 当前线程数
top -H -p <pid>                 # 查看进程线程情况
```

### 4. 查找句柄泄漏

```
ls /proc/<pid>/fd/ | wc -l      # 某个进程打开的FD数量
```

如果发现某个进程 FD 数持续上涨 → 可能存在句柄泄漏。

------

## 三、调整方法（RHEL 8）

### 1. 临时修改

```
ulimit -n 65535     # 调整最大文件句柄数
ulimit -u 16384     # 调整最大进程数
```

⚠️ 只对当前 shell 会话有效。

------

### 2. 永久修改（推荐）

#### 方法一：修改 **/etc/security/limits.conf**

```
vi /etc/security/limits.conf
```

添加：

```
用户名   soft  nofile  65535
用户名   hard  nofile  65535
用户名   soft  nproc   16384
用户名   hard  nproc   16384
```

> `soft` 是警告阈值，`hard` 是绝对上限。

#### 方法二：配置 systemd（RHEL 8 常用）

编辑服务的 unit 文件：

```
systemctl edit 服务名
```

添加：

```
[Service]
LimitNOFILE=65535
LimitNPROC=16384
```

然后重载并重启：

```
systemctl daemon-reexec
systemctl restart 服务名
```

------

### 3. 系统级别调整

- 修改系统最大文件数：

```
echo 2097152 > /proc/sys/fs/file-max
```

永久配置：

```
echo "fs.file-max=2097152" >> /etc/sysctl.conf
sysctl -p
```

------

## 四、总结

- **问题来源**：进程数过多 or 文件句柄数过多。
- **排查方法**：`ulimit`、`lsof`、`ps`、`/proc/pid/fd`。
- **解决方法**：合理修改 `ulimit`、`limits.conf`、`systemd LimitNOFILE/LimitNPROC`、`sysctl`。