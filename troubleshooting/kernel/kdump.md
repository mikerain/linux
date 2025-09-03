```
How to troubleshoot kernel crashes, hangs, or reboots with kdump on Red Hat Enterprise Linux
```

https://access.redhat.com/solutions/6038

The importance of configuring kernel dumps in Red Hat Enterprise Linux

https://www.redhat.com/en/blog/importance-configuring-kernel-dumps-rhel





```
配置和测试kdump的方法：

1.选定vmcore文件存放位置，最简单的就是本地文件系统。
如果不修改/etc/kdump.conf配置文件的话，默认会存放到/var/crash目录中，建议存放目录的可用空间要和物理内存比例为1：1，这样的要求是为了应对某些极端情况，比如物理内存全部被占满等。

2.设定crashkernel

在RHEL7及以上，默认值auto,默认不需要修改。如果系统中有很多第三方模块，那么建议设置crashkernel=768M,修改方法如下：
How do I permanently modify the kernel command line on RHEL 7? 
https://access.redhat.com/solutions/1136173

How do I permanently modify the kernel command line in RHEL 8? 
https://access.redhat.com/solutions/6383141

How to modify the kernel command-line in Red Hat Enterprise Linux 9 
https://access.redhat.com/solutions/6668601

3.设置kdump服务开机启动：
在RHEL7及以上：
~~~
systemctl enable kdump
~~~
由于配置kdump需要重启系统生效（加载crashkernel），所以还是建议您当配置生效后做个测试，以免耽误解决问题的时间。

4：在业务running时，测试kdump是否可以生成vcmore，测试的vmcore可直接删除。
请用“echo c > /proc/sysrq-trigger”命令测试kdump是否可以正常生成vmcore。注意:该命令会导致系统kernel panic后重启，如果kdump工作正常，在上面第一步中设置的地方，会有vmcore文件生成。

5:如果您之前已配置过kdump服务，则可执行下面的命令来确认kdump是否启用：
在RHEL7及以上：
~~~
systemctl status kdump
~~~
返回结果是：Kdump is operational或active，则说明kdump已经启用了，尽管如此，但还是建议您做个测试，以免耽误解决问题的时间（测试方法见第4点）。

详细内容可以参考以下文档:
How to troubleshoot kernel crashes, hangs, or reboots with kdump on Red Hat Enterprise Linux
https://access.redhat.com/node/6038
```



启动kdump

```
systemctl enable --now kdump

```

配置文件：

/etc/kdump.conf

一般会在这个文件时配置，将dump文件通过ssh发送到另一个主机上



dump文件位置：

/var/crash



触发dump

通过将 /proc/sys/kernel/sysrq ⽂件中的值设置为 1 来启⽤ SysRq 功能

通过将 /proc/sysrq-trigger ⽂件中的值设置为 c来触发系统崩溃。

```
echo 1 > /proc/sys/kernel/sysrq
echo c > /proc/sysrq-trigger
```



dump文件：

```
cd /var/crash/127.0.0.1-2025-08-22-02:44:42

[root@localhost 127.0.0.1-2025-08-22-02:44:42]# ls -la
total 53772
drwxr-xr-x. 2 root root       67 Aug 22 02:44 .
drwxr-xr-x. 3 root root       43 Aug 22 02:44 ..
-rw-------. 1 root root    66064 Aug 22 02:44 kexec-dmesg.log
-rw-------. 1 root root 54928072 Aug 22 02:44 vmcore
-rw-------. 1 root root    59506 Aug 22 02:44 vmcore-dmesg.txt
```



这三个文件就是 **kdump 崩溃转储的核心产物**，分别有不同用途。

------

## 1. `kexec-dmesg.log`

- **内容**：保存 `kdump` 第二内核（capture kernel）启动和运行时的日志。
- **作用**：帮助排查 kdump 本身的问题，比如：
  - kdump 是否成功预留了 crashkernel 内存
  - kdump 是否成功加载了 initramfs
  - dump 过程中是否出现错误
- **查看方式**：直接 `less kexec-dmesg.log`。

------

## 2. `vmcore`

- **内容**：这是最重要的 **内核崩溃内存转储文件**（二进制格式）。
  - 相当于“出事时整个内核内存的快照”
  - 包含进程状态、内核数据结构、调用栈等
- **作用**：用于深度分析崩溃原因（比如内核 panic、死锁、驱动 Bug 等）。
- **查看方式**：
   需要配合 **调试内核符号文件 (vmlinux)** 使用 `crash` 工具进行分析。

### 分析示例：

1. 安装工具：

   ```
   sudo dnf install crash kernel-debuginfo
   ```

   （要启用 debuginfo 仓库，例如 `subscription-manager repos --enable=rhel-8-for-x86_64-appstream-rpms --enable=rhel-8-for-x86_64-debug-rpms`）

2. 启动 crash 分析：

   ```
   sudo crash /usr/lib/debug/lib/modules/$(uname -r)/vmlinux /var/crash/.../vmcore
   ```

3. 常见命令：

   - `bt` → 查看崩溃进程的 **调用栈**
   - `ps` → 查看崩溃时的 **进程列表**
   - `log` → 查看内核日志
   - `files` → 查看进程打开的文件
   - `help` → crash 的帮助菜单

------

## 3. `vmcore-dmesg.txt`

- **内容**：这是从 `vmcore` 中提取出来的 **内核崩溃时的 dmesg 日志**。

- **作用**：快速了解崩溃时的内核信息，不必立刻用 crash 工具分析。

  - 例如看到 `Kernel panic - not syncing: Fatal exception`
  - 可以快速知道 panic 的触发点。

- **查看方式**：

  ```
  less vmcore-dmesg.txt
  ```

------

## 🔑 三个文件的关系总结

- **kexec-dmesg.log**：dump 过程中第二内核的日志（dump 工具运行情况）。
- **vmcore**：内核内存快照（原始核心文件，用于 crash 深度分析）。
- **vmcore-dmesg.txt**：从 vmcore 中提取的 dmesg 日志（快速查看崩溃原因）。

------

✅ **建议实践流程**：

1. 先看 `vmcore-dmesg.txt`，快速知道 panic 的触发点。
2. 如果需要更详细的上下文（堆栈、进程状态等），用 `crash` 加载 `vmcore` 分析。
3. 如果 dump 本身失败或不完整，查 `kexec-dmesg.log`。





### 在/etc/default/grub中配置crashkernel=auto和kdump有什么关系呢

`crashkernel=auto` 和 **kdump** 之间是紧密相关的：

------

## 1. kdump 的运行条件

- kdump 要求内核启动时 **预留一部分内存**，专门给 crash 内核（第二内核）使用。
- 如果没有预留内存，`kdump.service` 启动时会失败（报 `No crash kernel memory reservation found`）。

------

## 2. `crashkernel` 的作用

- 这是内核启动参数，用来告诉系统：启动时为 kdump 预留多少内存。
- 常见配置有：
  - `crashkernel=512M` → 固定预留 512MB
  - `crashkernel=2G` → 固定预留 2GB
  - `crashkernel=auto` → 由 RHEL 内核逻辑 **自动决定预留大小**

------

## 3. `crashkernel=auto` 的机制（RHEL 8 特有增强）

- 从 RHEL 7.5 开始，RHEL 内核支持 `crashkernel=auto`。

- 其逻辑是根据系统总内存大小，自动分配合适的预留空间：

  | 系统内存大小 | 预留空间大小 (约) |
  | ------------ | ----------------- |
  | < 2 GB       | 192 MB            |
  | 2–6 GB       | 256 MB            |
  | 6–8 GB       | 512 MB            |
  | 8–64 GB      | 1 GB              |
  | > 64 GB      | 2 GB 或更多       |

- 这样避免了管理员手工计算和配置。

------

## 4. 两者关系总结

- **kdump 要能正常工作，必须有 crashkernel 预留内存**。
- `crashkernel=auto` 就是让内核 **自动选择**合适的预留大小，保证 kdump 正常运行。
- 如果不设置 `crashkernel`，`kdump.service` 启动会失败，捕获不了内核崩溃。
- 如果配置了 `crashkernel=auto`，你通常不用再手工指定 `512M` 或 `2G`，系统会自动选择。

------

## 5. 验证方法

重启后，查看是否预留成功：

```
dmesg | grep -i crash
```

你会看到类似：

```
Kernel command line: ... crashkernel=auto ...
Reserving 512MB of memory at 0x0000000fc000000 for crashkernel
```

------

✅ **一句话总结**：
 `crashkernel=auto` 是为 **kdump 自动预留内存** 的方式；没有 crashkernel（auto 或固定值），kdump 无法工作。