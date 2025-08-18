 **`nmcli` 常用命令总结**（适用于 RHEL/CentOS/Fedora 等使用 NetworkManager 的系统）：

------

## 1️⃣ 基础信息查看

```
nmcli device                # 查看所有网络设备及状态
nmcli connection show       # 查看所有连接配置
nmcli connection show <名称> # 查看某个连接的详细信息
nmcli general status        # 查看网络管理器整体状态
nmcli -p device show        # 显示所有设备的详细信息（带格式化）
```

------

## 2️⃣ 连接管理

```
nmcli connection up <名称>     # 启用某个连接
nmcli connection down <名称>   # 禁用某个连接
nmcli connection delete <名称> # 删除连接配置
nmcli connection modify <名称> ipv4.method auto   # 设置 DHCP
nmcli connection modify <名称> ipv4.method manual ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns 8.8.8.8
                              # 修改为静态 IP
```

------

## 3️⃣ 创建新连接

```
# 创建有线连接
nmcli connection add type ethernet ifname eth0 con-name my-eth0 autoconnect yes

# 创建静态 IP 连接
nmcli connection add type ethernet ifname eth0 con-name static-ip \
    ipv4.addresses 192.168.1.100/24 \
    ipv4.gateway 192.168.1.1 \
    ipv4.dns 8.8.8.8 \
    ipv4.method manual

# 创建 Wi-Fi 连接
nmcli device wifi connect "SSID名称" password "密码"
```

------

## 4️⃣ Wi-Fi 操作

```
nmcli device wifi list                      # 扫描并列出可用 Wi-Fi
nmcli device wifi connect "SSID" password "密码"  # 连接 Wi-Fi
nmcli connection modify <名称> wifi-sec.key-mgmt wpa-psk  # 修改 Wi-Fi 认证方式
```

------

## 5️⃣ 设备操作

```
nmcli device status         # 查看设备状态
nmcli device disconnect eth0 # 断开设备
nmcli device connect eth0    # 连接设备
nmcli device reapply eth0    # 重新应用当前设置
```

------

## 6️⃣ 常用调试

```
nmcli general logging level DEBUG domains ALL  # 开启调试日志
nmcli -f GENERAL,WIFI-PROPERTIES device show wlan0  # 查看无线网卡详细信息
nmcli connection reload       # 重新加载配置
```

------

✅ **小结**

- `nmcli device` → 操作网卡设备（硬件层）
- `nmcli connection` → 操作网络连接配置（软件层）
- 创建连接时，`ifname` 必须是网卡名称
- 修改 IP/网关/DNS 等信息要用 `nmcli connection modify`



修改 connection name

nmcli c m 'System eth0' connection.id 'eth0'

```
[root@localhost ~]# nmcli c 
NAME         UUID                                  TYPE      DEVICE 
System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0   

[root@localhost ~]# nmcli c m 'System eth0' connection.id 'eth0'
[root@localhost ~]# nmcli connection reload

[root@localhost ~]# nmcli c s
NAME  UUID                                  TYPE      DEVICE 
eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0   

```

