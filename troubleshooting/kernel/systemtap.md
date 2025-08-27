内核调试

## 1. SystemTap 的用途

SystemTap 是一种 **动态追踪（dynamic tracing）工具**，主要用于 **诊断、分析和调试 Linux 内核及用户空间应用的问题**，而不需要重新编译内核。

主要用途：

- **性能分析**
  - 跟踪 CPU 调度、系统调用、I/O、内存分配等。
  - 定位性能瓶颈。
- **故障排查**
  - 查看某个内核函数的调用频率和调用栈。
  - 跟踪内核 panic 之前的系统状态。
- **内核调试**
  - 捕捉异常事件（如页错误、进程挂起）。
  - 分析内核模块行为。
- **系统监控**
  - 实时收集系统运行数据（类似 DTrace 的功能）。



安装

```
subscription-manager repos --enable rhel-8-for-$(uname-i)-baseos-debug-rpms
subscription-manager repos --enable rhel-8-for-$(uname-i)-baseos-source-rpms
subscription-manager repos --enable rhel-8-for-$(uname-i)-appstream-debug-rpms
subscription-manager repos --enable rhel-8-for-$(uname-i)-appstream-source-rpms


sudo dnf install -y systemtap systemtap-runtime
sudo dnf install -y kernel-debuginfo kernel-debuginfo-common kernel-devel
```



## 基本使用方法

### (1) 简单脚本

示例：统计 `sys_open` 系统调用次数

```
stap -v -e 'probe syscall.open { println("open called by pid " . pid()) }'
```

### (2) 跟踪内核函数

```
stap -e 'probe kernel.function("do_sys_open") { println("pid=" . pid() . " open " . user_string($filename)) }'
```

### (3) 使用内置脚本

SystemTap 提供了很多现成的脚本：

```
stap-pretty-print /usr/share/systemtap/examples
```

例如：

- 监控 I/O 延迟：

  ```
  stap /usr/share/systemtap/examples/io/iotime.stp
  ```

- 监控上下文切换：

  ```
  stap /usr/share/systemtap/examples/process/schedtimes.stp
  ```

------

## 