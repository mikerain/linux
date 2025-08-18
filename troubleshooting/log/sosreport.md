## 安装

```
yum install sos
```

```
sos report
sos report -o 
```



## 收集

sosreport 允许用户通过插件机制选择性地收集特定部分的信息。这可以通过 `-e` 或 `--enable-plugins` 参数来指定只启用某些插件，或者使用 `-o` 或 `--only-plugins` 参数来指定仅运行某些插件（禁用所有其他插件）。以下是具体方法：

查看支持哪些插件

```
sos report --list-plugins
```



\### **总结命令**

| **需求**                           | **命令示例**                                                                 |

|------------------------------------|-----------------------------------------------------------------------------|

| 只收集日志和进程                   | `sudo sos report --only-plugins=logs,processes`                             |

| 只收集网络和防火墙                 | `sudo sos report --only-plugins=networking,firewalld`                       |

| 收集除内核和硬件外的所有信息       | `sudo sos report --skip-plugins=kernel,hardware`                            |

| 最小收集（仅日志+进程）+ 非交互    | `sudo sos report --only-plugins=logs,processes --batch --no-report`         |

通过选择性收集，你可以生成更小、更聚焦的 `sosreport`，加快收集速度并减少需要传输的数据量。

是的，`sosreport` 支持选择性收集特定部分的信息，这可以通过 `--only-plugins` 参数实现。这在需要保护敏感数据、减少报告体积或聚焦特定问题时非常有用。以下是详细方法和示例：

------



------

### 常用插件组合

| 场景               | 命令                                                     |
| :----------------- | :------------------------------------------------------- |
| 性能问题           | `--only-plugins=process,kernel,logs`                     |
| 网络故障           | `--only-plugins=networking,logs`                         |
| 存储异常           | `--only-plugins=storage,hardware,kernel`                 |
| 服务启动失败       | `--only-plugins=system,logs`                             |
| 安全审计           | `--only-plugins=selinux,logs,audit`                      |
| 最小化敏感数据收集 | `--only-plugins=logs,kernel` (避免收集网络配置/用户数据) |

------

### 高级选项

#### 1. 排除特定插件（反选）

```
# 收集除yum和网络外的所有信息
sudo sos report --skip-plugins=yum,networking
```

#### 2. 增加插件详细级别

```
# 对存储插件收集额外调试信息
sudo sos report --only-plugins=storage --plugin-option=storage.verbose=enable
```

#### 3. 自定义日志时间范围

```
# 只收集最近2小时的日志
sudo sos report --only-plugins=logs --log-size=2
```

------

### 注意事项

1. **核心插件始终包含**：`core` 插件（主机名/日期等基础信息）无法禁用
2. **依赖关系**：部分插件依赖其他插件（如 `docker` 依赖 `cgroups`）
3. **大小控制**：选择性收集可显著减小报告体积（完整报告通常 50-500MB，选择性可降至 5-50MB）
4. **敏感数据**：仍需人工检查，某些插件（如 `logs`）可能仍包含敏感信息

> 💡 提示：使用 `--batch` 参数可跳过交互式确认，适合自动化收集。生成后通过 `tar tf report.tar.xz` 快速验证包含的内容。



## 解读报告

sos report解压后的目录

```
tar xf sosreport-localhost-2025-08-05-magyeut.tar.xz
```



```
sosreport-hostname-xxxx-2024-08-06-abcd/
├── sos_logs/             # ⭐ sosreport 自身日志（检查是否采集完整）
├── sos_reports/          # ⭐ 核心报告目录（文本格式，可直接查看）
│   ├── dmesg.txt         # ⭐ 内核环形缓冲区日志（启动/运行时错误）
│   ├── journals/         # ⭐ systemd 日志（按服务分类）
│   │   ├── system.journal
│   │   └── ... 
│   ├── lvm/              # LVM 配置和状态
│   ├── mdadm/            # 软件 RAID 状态
│   ├── networking/       # ⭐ 网络配置（网卡、路由、防火墙）
│   │   ├── ip_addr.txt
│   │   ├── route.txt
│   │   ├── iptables/
│   │   └── ...
│   ├── processes/        # ⭐ 进程信息（ps, pid 等）
│   ├── rpm/              # ⭐ 所有安装的 RPM 包列表
│   ├── selinux/          # SELinux 状态和日志
│   ├── services/         # ⭐ systemd 服务状态
│   ├── sys/              # 系统信息（设备、模块、文件系统）
│   │   ├── block/        # 块设备信息
│   │   ├── kernel/       # 内核参数（sysctl）
│   │   ├── modules/      # ⭐ 已加载内核模块
│   │   └── vmstat.txt    # ⭐ 虚拟内存统计
│   ├── var/              # ⭐ 重要日志文件（从 /var/log 复制）
│   │   ├── log/messages  # ⭐ 核心系统日志
│   │   ├── log/secure    # ⭐ 认证和安全日志
│   │   ├── log/audit/    # ⭐ auditd 审计日志（如启用）
│   │   └── ...
├── sos_commands/         # ⭐ 命令输出目录（执行过的命令结果）
│   ├── date              # 报告生成时间
│   ├── df                # ⭐ 磁盘空间使用
│   ├── free              # ⭐ 内存使用
│   ├── hostname          # 主机名
│   ├── ip_-s_link        # ⭐ 网络接口统计信息
│   ├── lscpu             # ⭐ CPU 架构信息
│   ├── lspci             # ⭐ PCI 设备列表
│   ├── lsblk             # ⭐ 块设备列表
│   ├── ps                # ⭐ 进程快照
│   ├── sestatus          # SELinux 状态
│   ├── ss_-tulpn         # ⭐ 网络连接和监听端口
│   ├── systemctl_list-units  # ⭐ 所有 systemd 单元状态
│   ├── top               # ⭐ 进程资源使用（快照）
│   ├── uname             # 内核版本
│   ├── uptime            # 系统运行时间
│   └── ... 
├── sos_strings/          # 特定配置文件的字符串提取
└── ui/                   # HTML 报告（可视化查看）
```





### **核心分析方法**

#### **📍 通用检查（先看这里！）**

1. **系统概览 (`sos_commands/`):**
   - `uptime`：系统负载和运行时间（高负载？）
   - `date`：报告生成时间（是否与问题时间相符？）
   - `uname -a`：内核版本和架构
   - `lscpu`：CPU 详细信息（型号、核心数）
   - `free -m`：内存使用（是否耗尽？Swap 使用？）
   - `df -h`：磁盘空间使用（根分区 `/` 是否满？）
   - `lsblk`：块设备布局（磁盘、分区、LVM、RAID）
   - `top` / `ps auxf`：进程列表（CPU/内存消耗高的进程？异常进程？）
   - `rpm -qa`：安装的软件包（确认关键软件版本）
2. **日志文件 (`var/log/`, `sos_reports/journals/`):**
   - `/var/log/messages`：**最重要的系统日志**（过滤问题时间段的 `ERROR`, `WARNING`, `CRITICAL`, `Failed`, `panic`, `oom-killer`）。
   - `/var/log/secure`：认证日志（SSH 登录、sudo、用户切换）。
   - `/var/log/audit/audit.log`：SELinux 或策略审计日志（权限问题）。
   - `journalctl` 输出 (`sos_reports/journals/`): 使用 `journalctl --since "YYYY-MM-DD HH:MM:SS" --until "YYYY-MM-DD HH:MM:SS" -p err..alert` 模拟过滤特定服务和时间段的错误。
3. **内核信息 (`dmesg`, `sys/`):**
   - `dmesg`：检查启动期间和运行时的**硬件错误、驱动问题、文件系统错误、OOM（内存不足）杀手活动、内核崩溃**。
   - `/sys/module/*`：查看已加载的内核模块及其参数。

#### **📍 按问题类型深入排查**

- **性能问题 (慢、卡顿、无响应):**
  - `top`/`ps`：CPU/Memory 消耗 Top 进程。
  - `vmstat -s`：系统级内存统计（页入/页出高？）。
  - `ip -s link` / `ifconfig`：网络接口错误包 (`RX-ERR`, `TX-ERR`, `dropped`)。
  - `iostat -xz 1`：磁盘 I/O 延迟 (`await`)、利用率 (`%util`)、队列长度。
  - `sar` 数据 (如果安装了 `sysstat` 且在有效期内)。
  - 检查 `/var/log/messages` 是否有 OOM 日志。
- **网络问题 (无法连接、丢包、慢):**
  - `ip addr` / `ifconfig`：接口状态和 IP 配置。
  - `ip route`：路由表。
  - `ss -tulpn`：监听端口和活动连接（确认服务在监听）。
  - `firewall-cmd --list-all` / `iptables-save`：防火墙规则。
  - `ip -s link`：接口统计信息（错误、丢包）。
  - `/etc/resolv.conf`：DNS 配置。
  - `sysctl -a | grep net`：网络内核参数。
- **存储问题 (磁盘满、I/O 错误、文件系统只读):**
  - `df -h` / `lsblk`：空间和挂载点。
  - `dmesg`：查找 `I/O error`, `EXT4-fs error`, `remount-ro` 等关键字。
  - `/var/log/messages`：文件系统错误日志。
  - `lvs` / `pvs` / `vgs`：LVM 状态。
  - `cat /proc/mdstat`：软件 RAID 状态。
  - `smartctl` 输出 (如果收集了)：磁盘健康状态。
- **服务启动失败 (`systemctl status xxx` 报错):**
  - `systemctl status servicename`：服务的详细状态和最后日志片段。
  - `journalctl -u servicename`：服务的完整 systemd 日志（在 `sos_reports/journals/` 找对应文件）。
  - 检查服务依赖项 (`systemctl list-dependencies servicename`)。
  - 检查端口冲突 (`ss -tulpn | grep :port`）。
  - 检查配置文件语法 (相关服务的配置文件在 `/etc` 下)。
  - 检查 SELinux 拒绝 (`/var/log/audit/audit.log`, 使用 `ausearch` 或 `sealert`)。
- **系统启动问题:**
  - `dmesg`：**核心**，看内核启动阶段报错。
  - `/var/log/boot.log`：系统服务启动日志。
  - `journalctl -b`：本次启动的 systemd 日志。
  - 检查 `/boot` 分区是否满 (`df -h /boot`)。
  - 检查 GRUB 配置 (`/boot/grub2/grub.cfg`)。
- **硬件相关问题:**
  - `dmesg`：**首要位置**，查找硬件检测失败、驱动加载错误、PCIe/AHCI/SATA/USB 错误、EDAC 内存错误。
  - `lspci -vvv`：PCI 设备详细信息（包括驱动信息）。
  - `lsusb`：USB 设备信息。
  - `dmidecode`：SMBIOS/DMI 信息（主板、内存条、BIOS 版本）。
  - `/sys/class/dmi/id/*`：DMI 信息文件。
  - `smartctl -a /dev/sdX`：磁盘 SMART 健康信息（如果收集了）。

------

### **4. 利用工具辅助分析**

- **`less` / `grep` / `awk` / `sort` / `uniq`：** 命令行文本处理神器。

  ```
  grep -i "error\|fail\|warn" var/log/messages # 过滤关键错误
  awk '{print $1}' sos_commands/ip_-s_link | sort | uniq -c # 统计网络错误
  ```

- **`vi` / `vim` / `nano`：** 查看配置文件。

- **HTML 报告 (`ui/index.html`):** 提供可视化导航（需浏览器打开），适合快速浏览和搜索。

------

### **5. 解读技巧与注意事项**

1. **时间戳是生命线：** 所有日志和命令输出都带有时间戳。**先确认报告生成时间**，并**聚焦于问题发生时间段**的数据。使用 `grep` 按时间范围过滤日志。
2. **由表及里：** 从系统概览 (`uptime`, `free`, `df`, `top`) 和核心日志 (`messages`, `dmesg`) 开始，发现异常点后再深入特定命令或日志文件。
3. **相关性分析：** 一个问题往往在多个地方留下痕迹。例如：
   - 应用报错 (`/var/log/messages`) + 端口未监听 (`ss -tulpn`) = 服务未启动。
   - 系统变慢 (`top` 高 `%wa`) + 磁盘 I/O 高 (`iostat` 高 `await`) + `dmesg` 磁盘错误 = 磁盘故障。
4. **版本与环境：** 注意 RHEL 8 使用 `dnf` 而非 `yum`，默认使用 `firewalld` 而非 `iptables`（底层仍是 `nftables`），核心日志在 `journald`。
5. **SELinux：** 遇到权限问题时，务必检查 `/var/log/audit/audit.log` 或使用 `sealert -a /var/log/audit/audit.log`。
6. **对比分析：** 如果可能，与一个正常系统的 `sosreport` 做对比（注意配置差异）。
7. **善用搜索：** 在解压目录中使用 `grep -r "keyword" .` 全局搜索关键字。
8. **注意权限：** 部分文件（如某些日志）可能需要 `root` 权限查看（`sudo` 或切换到 `root`）。
9. **`sos.log`：** 查看 `sos_logs/sos.log` 了解 `sosreport` 收集过程中是否有错误或遗漏。

------

### **总结步骤**

1. **解压报告。**
2. **浏览 `sos_commands/` 核心命令输出 (`free`, `df`, `top`, `uptime`, `ps`, `ss`, `dmesg`)，获取系统整体状态。**
3. **重点检查 `/var/log/messages` 和 `dmesg` 核心日志，按问题发生时间过滤错误和警告。**
4. **根据初步发现的线索（如 CPU 高、内存不足、磁盘满、网络错误、服务名），深入到相关目录 (`sos_commands/networking/`, `sos_commands/processes/`, `sos_reports/services/`, `sys/block/`, `sys/kernel` 等) 查看详细命令输出或日志。**
5. **结合多个来源的信息进行关联分析，定位根本原因。**
6. **(可选) 使用 `ui/index.html` 辅助导航和搜索。**

通过系统性地检查这些关键部分，你就能有效地解读 `sosreport`，诊断出 RHEL 8 系统的问题根源。遇到具体错误时，结合日志中的关键信息和相关命令输出进行深度分析是关键。



