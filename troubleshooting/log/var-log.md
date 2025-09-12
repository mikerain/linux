在 RHEL 8 系统中，`/var/log` 目录是系统日志的核心存储位置。以下是主要日志文件及其用途，以及排查系统故障时应重点关注的日志：

------

### **核心日志文件概览**

| 日志文件路径                   | 用途说明                                                | 故障排查重点                                                |
| :----------------------------- | :------------------------------------------------------ | :---------------------------------------------------------- |
| **`/var/log/messages`**        | **最全面的系统日志** 记录内核、服务、系统工具等通用信息 | • 系统级错误和警告 • 服务启动失败 • 硬件检测问题 • 内核异常 |
| **`/var/log/secure`**          | **安全认证日志** 记录用户登录、sudo、SSH 等安全事件     | • 登录失败 • 暴力破解尝试 • 权限变更 • sudo 命令执行        |
| **`/var/log/audit/audit.log`** | **SELinux 审计日志** 记录系统调用和策略拒绝事件         | • SELinux 权限拒绝 • 文件/进程访问违规 • 安全策略违规       |
| **`/var/log/boot.log`**        | 系统启动日志                                            | • 启动阶段服务失败 • 硬件初始化错误 • 系统无法启动          |
| **`/var/log/dnf.log`**         | 软件包管理日志 (RHEL 8 使用 DNF 替代 YUM)               | • 软件安装/更新失败 • 依赖关系冲突 • 仓库配置问题           |
| **`/var/log/cron`**            | 计划任务日志                                            | • cron 任务执行失败 • 任务超时 • 权限问题                   |
| **`/var/log/maillog`**         | 邮件系统日志                                            | • 邮件发送/接收失败 • SMTP 服务异常                         |
| **`/var/log/httpd/`**          | Apache Web 服务日志 (目录包含 access_log/error_log)     | • HTTP 服务崩溃 • 5xx 服务器错误 • 访问拒绝                 |
| **`/var/log/journal/`**        | **systemd 二进制日志目录** 使用 `journalctl` 命令查看   | • 服务启动失败 • 进程崩溃 • 资源不足错误                    |

------

### **排查系统故障的日志检查指南**

#### **1. 系统崩溃/无法启动**

- **首要检查**：

  ```
  journalctl -b -1  # 查看上一次启动日志
  journalctl -xb    # 查看当前启动日志（含解释信息）
  ```
  
- **关键文件**：

  - `/var/log/boot.log`：启动过程中的服务错误
  - `/var/log/dmesg`：内核启动信息（硬件检测问题）
  - `/var/log/messages`：搜索 `panic`, `Oops`, `segfault`

#### **2. 服务无法启动**

- **使用 systemd 日志**：

  ```
journalctl -u httpd.service --since "2025-08-06 09:00"  # 查看指定服务日志
  ```

- **关键文件**：

  - `/var/log/messages`：搜索服务名 + `failed`
  - `/var/log/secure`：检查权限问题（如无法绑定端口）

#### **3. 性能问题（卡顿、负载高）**

- **检查资源瓶颈**：

  ```
grep -i "oom-killer" /var/log/messages        # 内存不足事件
  grep -i "timeout" /var/log/messages           # 操作超时
journalctl --since "09:00" | grep "CPU usage" # CPU 过载
  ```

- **关键文件**：

  - `/var/log/messages`：搜索 `load average`, `swap full`, `I/O error`
  - `/var/log/sa/sa*`（若安装 sysstat）：历史性能数据

#### **4. 网络问题**

- **连接诊断**：

  ```
  grep "DROP" /var/log/audit/audit.log          # 防火墙拦截
  grep "Connection refused" /var/log/messages   # 服务未监听
  ```
  
- **关键文件**：

  - `/var/log/messages`：网络接口错误
  - `/var/log/secure`：SSH 连接失败
  - `/var/log/httpd/error_log`：Web 服务连接问题

#### **5. 硬件故障**

- **硬件错误检测**：

  ```
dmesg | grep -i "error"                      # 实时内核错误
  grep -i "hard error" /var/log/messages       # 磁盘错误
grep -i "thermal" /var/log/messages          # CPU 过热
  ```

- **关键文件**：

  - `/var/log/messages`：硬件驱动错误
  - `/var/log/boot.log`：启动时硬件初始化失败
  - `/var/log/dmesg`：内核级硬件事件

#### **6. 安全事件**

- **入侵检测**：

  ```
grep "Failed password" /var/log/secure        # SSH 暴力破解
  grep "invalid user" /var/log/secure           # 非法用户尝试
ausearch -k suspicious-login                  # 审计可疑登录
  ```

- **关键文件**：

  - `/var/log/secure`：认证事件
  - `/var/log/audit/audit.log`：权限变更记录
  - `/var/log/firewalld`：防火墙拦截日志

------

### **日志分析技巧**

1. **时间过滤**：

   ```
   grep "Aug  6 10:" /var/log/messages      # 精确到小时
   journalctl --since "09:00" --until "10:00"
   ```
   
2. **优先级过滤**：

   ```
   journalctl -p err..emerg                # 只看错误及以上日志
   grep -E "err|crit|alert|emerg" /var/log/messages
   ```
   
3. **服务关联分析**：

   ```
journalctl _PID=$(pgrep httpd)          # 查看指定进程所有日志
   ```

4. **实时监控**：

   ```
   tail -f /var/log/messages               # 实时滚动查看
   journalctl -f -u nginx.service          # 跟踪特定服务
```

------

### **日志管理注意事项**

1. **日志轮转**：

   - 旧日志会压缩保存（如 `messages-20250801.gz`）
   - 配置：`/etc/logrotate.conf`

2. **二进制日志查看**：

```
   journalctl --list-boots                 # 查看所有启动会话
   journalctl --disk-usage                 # 检查日志占用空间
   ```
   
3. **日志保留策略**：

   - 修改 `/etc/systemd/journald.conf`：

   ```
     [Journal]
     SystemMaxUse=1G       # 限制最大磁盘占用
     MaxRetentionSec=1month # 保留时间
     ```

> **关键提示**：75% 的系统故障可通过以下三个日志快速定位：
>
> 1. `journalctl -xb`（启动问题）
> 2. `grep -i "error" /var/log/messages`（系统级错误）
> 3. `tail -f /var/log/secure`（认证问题）







**/var/log 下的特殊文件**：

| 文件                | 生成方式                          | 是否依赖 rsyslog |
| :------------------ | :-------------------------------- | :--------------- |
| `/var/log/boot.log` | **systemd-bootlogd 服务**直接生成 | ❌ 独立           |
| `/var/log/wtmp`     | 由 login 程序直接写入             | ❌ 独立           |
| `/var/log/btmp`     | 由 SSH/PAM 直接写入               | ❌ 独立           |
| `messages/secure`   | **不存在**（因无 rsyslog）        | ✅ 依赖           |







```
 less var/log/messages | egrep -i "INFO: task| BUG:|out of memory|segfault|page allocation|fork|oom-killer|soft lockup|blocked for more than|not responding" | wc -l
```





find reboot , restart log

- `/var/log/messages` shows below logs post system reboot in `startup logs` for multiple system reboot frequently.

[Raw](https://access.redhat.com/solutions/5680431#)

```
$ grep -i reboot messages
```