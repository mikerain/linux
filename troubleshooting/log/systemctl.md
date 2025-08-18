掌握 `systemctl` 命令是高效管理 Linux 服务的核心技能。以下是针对服务发现、配置文件定位、日志查看等场景的系统化命令集整理，基于 **RHEL/CentOS 7+** 环境（适配 `systemd` 架构）：

------

### 🔍 **一、服务发现与查询**

#### 📌 1. **列出所有服务**

- **列出已加载的服务**（含状态）26：

  ```
  systemctl list-units --type=service          # 仅活跃服务
  systemctl list-units --type=service --all    # 包含非活跃服务
  ```

- **列出所有可用的服务单元**（含启用状态）6：

  ```
  systemctl list-unit-files --type=service
  ```

#### 🔎 2. **按名称过滤服务**

- 使用 `grep` 快速查找特定服务名：

  ```
  systemctl list-units --type=service --all | grep nginx   # 按关键词过滤
  systemctl list-unit-files 'nginx*'                      # 通配符匹配:cite[10]
  ```

#### ⚠️ 3. **检查失败的服务**

```
systemctl --failed    # 显示启动失败的服务:cite[1]
```

------

### 📂 **二、配置文件定位**

#### 📍 1. **查看服务配置文件路径**

- **默认路径**：

  - 系统安装的服务：`/usr/lib/systemd/system/*.service`
  - 自定义或覆盖配置：`/etc/systemd/system/*.service` 37

- **直接查看服务文件内容**：

  ```
  systemctl cat nginx.service    # 显示完整配置（含覆盖文件）:cite[1]
  ```

#### ⚙️ 2. **修改配置后的操作**

- 重载 `systemd` 配置（不重启服务）：

  ```
  sudo systemctl daemon-reload    # 修改配置文件后必须执行:cite[1]:cite[3]
  ```

#### 📋 **配置文件位置速查表**

| **类型**     | **路径**                   | **优先级** | **用途**             |
| :----------- | :------------------------- | :--------- | :------------------- |
| 系统默认     | `/usr/lib/systemd/system/` | 低         | RPM 包安装的服务     |
| 运行时配置   | `/run/systemd/system/`     | 中         | 临时覆盖配置         |
| 管理员自定义 | `/etc/systemd/system/`     | 高         | 用户自定义或启用文件 |

------

### 📜 **三、日志查看与分析**

#### 🔍 1. **查看服务日志**

- **基本命令**：

  ```
  journalctl -u nginx.service      # 查看指定服务的所有日志:cite[4]:cite[8]
  journalctl -u nginx -f           # 实时跟踪日志输出
  ```

- **按时间筛选**：

  ```
  journalctl -u nginx --since "2025-07-01" --until "2025-07-05"
  ```

#### ⚠️ 2. **诊断启动失败**

- 查看详细错误信息（含进程退出码）：

  ```
  journalctl -u nginx -xe          # 显示最近错误日志（推荐）:cite[4]
  systemctl status nginx -l        # 显示完整状态（无分页）:cite[1]
  ```

------

### ⚙️ **四、服务管理命令集**

| **操作**     | **命令**                                  | **说明**                   |
| :----------- | :---------------------------------------- | :------------------------- |
| **启停控制** | `sudo systemctl start/stop/restart nginx` | 启动/停止/重启服务         |
| **开机自启** | `sudo systemctl enable/disable nginx`     | 启用/禁用开机启动          |
| **状态检查** | `systemctl status nginx`                  | 服务运行状态（含日志片段） |
| **依赖查看** | `systemctl list-dependencies nginx`       | 服务的依赖树6              |
| **环境变量** | `systemctl show-environment`              | 查看 `systemd` 环境变量3   |

------

### 🛠️ **五、高级调试技巧**

1. **测试服务启动过程**：

   ```
   systemd-analyze verify nginx.service  # 检查单元文件语法
   ```

2. **分析启动性能**：

   ```
   systemd-analyze blame                 # 按耗时排序服务启动时间:cite[1]
   systemd-analyze critical-chain nginx  # 查看服务启动关键路径:cite[1]
   ```

3. **临时修改启动参数**：

   ```
   sudo systemctl edit nginx.service     # 创建覆盖文件（存于 `/etc/systemd/system/`）:cite[1]
   ```

------

### 💎 **总结：高效排查流程**

1. **定位服务** → `systemctl list-units --type=service | grep <关键词>`
2. **查看配置** → `systemctl cat <服务名>.service`
3. **检查状态** → `systemctl status -l <服务名>`显示日志时换行
4. **分析日志** → `journalctl -u <服务名> -xe`
5. **依赖检查** → `systemctl list-dependencies <服务名>`

> 💡 **提示**：所有 `journalctl` 日志由 `systemd-journald` 统一管理，无需额外配置 `syslog` 8。若需持久化存储日志，可配置 `/etc/systemd/journald.conf` 中的 `Storage=persistent`。