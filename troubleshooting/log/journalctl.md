服务启动：

```
systemctl cat systemd-journald.service
[root@localhost journal]# systemctl cat systemd-journald.service
# /usr/lib/systemd/system/systemd-journald.service
#  SPDX-License-Identifier: LGPL-2.1+
#
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Journal Service
Documentation=man:systemd-journald.service(8) man:journald.conf(5)
DefaultDependencies=no
Requires=systemd-journald.socket
After=systemd-journald.socket systemd-journald-dev-log.socket systemd-journald->
Before=sysinit.target

[Service]
Type=notify
Sockets=systemd-journald.socket systemd-journald-dev-log.socket
ExecStart=/usr/lib/systemd/systemd-journald

```

配置文件：

```
 cat /etc/systemd/journald.conf
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
#
# Entries in this file show the compile time defaults.
# You can change settings by editing this file.
# Defaults can be restored by simply deleting this file.
#
# See journald.conf(5) for details.

[Journal]
#Storage=auto
#Compress=yes
#Seal=yes
#SplitMode=uid
#SyncIntervalSec=5m
#RateLimitIntervalSec=30s
#RateLimitBurst=10000
#SystemMaxUse=
#SystemKeepFree=
#SystemMaxFileSize=
#SystemMaxFiles=100
#RuntimeMaxUse=
#RuntimeKeepFree=
#RuntimeMaxFileSize=
#RuntimeMaxFiles=100
#MaxRetentionSec=
#MaxFileSec=1month
#ForwardToSyslog=no
#ForwardToKMsg=no
#ForwardToConsole=no
#ForwardToWall=yes
#TTYPath=/dev/console
#MaxLevelStore=debug
#MaxLevelSyslog=debug
#MaxLevelKMsg=notice
#MaxLevelConsole=info
#MaxLevelWall=emerg

```

日志位置：

`journald` 可以将日志存储在内存（默认）或磁盘，取决于 `Storage=` 配置：

- **内存日志（默认）：**

  ```
  /run/log/journal/
  ```

（临时，关机后丢失）

**磁盘日志（如果启用 persistent）：**

```
/var/log/journal/
```





日志是以二进制格式保存的，不能直接用 `cat` 查看。查看方法如下：

```
journalctl                      # 查看所有日志
journalctl -b                  # 查看本次启动日志
journalctl -u sshd             # 按服务查看
journalctl -xe                 # 查看最近的

journalctl _SYSTEMD_UNIT=sshd.service #查看 SSH 日志
```



| 排查目标 | 命令                                          |
| :------- | :-------------------------------------------- |
| 启动故障 | `journalctl -xb -p3`                          |
| 服务崩溃 | `journalctl -u service.name --since "1h ago"` |
| 内存不足 | `journalctl -g "oom-killer"`                  |
| 认证问题 | `journalctl _SYSTEMD_UNIT=sshd.service`       |

## 小结

| 项目         | 路径或命令说明                                              |
| ------------ | ----------------------------------------------------------- |
| 启动服务文件 | `/usr/lib/systemd/system/systemd-journald.service`          |
| 配置文件     | `/etc/systemd/journald.conf`                                |
| 日志路径     | `/run/log/journal/`（默认）或 `/var/log/journal/`（持久化） |
| 状态查看     | `systemctl status systemd-journald`                         |
| 日志查看     | `journalctl` 系列命令                                       |

日志行太长时，使用no-pager会自动换行

```
journalctl -u nginx.service --no-pager
```



```
journalctl -u nginx --since "10 min ago" --until "now"
```

