以下是 RHEL 8 中 `ip` 命令（来自 `iproute2` 包）的常见用法示例，涵盖 IP 地址、网关、路由和 DNS 配置：

------

### **1. 查看网络信息**

```
# 查看所有接口简要信息
ip link show

# 查看详细 IP 配置（包括 IPv4/IPv6）
ip address show       # 简写: ip a

# 查看路由表
ip route show        # 简写: ip r

# 查看 ARP 缓存
ip neigh show
```

------

### **2. IP 地址管理**

#### **临时添加/删除 IP 地址（重启失效）**

```
# 添加 IPv4 地址到 enp0s3
sudo ip address add 192.168.1.100/24 dev enp0s3

# 添加 IPv6 地址
sudo ip address add 2001:db8::1/64 dev enp0s3

# 删除 IP 地址
sudo ip address del 192.168.1.100/24 dev enp0s3
```

#### **永久配置 IP**

> ⚠️ 使用 `ip` 命令的配置是临时的。永久生效需修改配置文件：
>
> - **传统方式**: 编辑 `/etc/sysconfig/network-scripts/ifcfg-enp0s3`
> - **推荐方式 (RHEL 8)**: 使用 `nmcli` 或 `nmtui`（NetworkManager 工具）

------

### **3. 网关/默认路由**

```
# 添加默认网关 (IPv4)
sudo ip route add default via 192.168.1.1 dev enp0s3

# 添加默认网关 (IPv6)
sudo ip -6 route add default via 2001:db8::1 dev enp0s3

# 删除默认网关
sudo ip route del default via 192.168.1.1
```

------

### **4. 静态路由管理**

```
# 添加静态路由 (访问 10.0.0.0/24 通过网关 192.168.1.2)
sudo ip route add 10.0.0.0/24 via 192.168.1.2 dev enp0s3

# 添加 IPv6 静态路由
sudo ip -6 route add 2001:db8:1::/64 via 2001:db8::2 dev enp0s3

# 删除静态路由
sudo ip route del 10.0.0.0/24
```

------

### **5. DNS 配置**

> ⚠️ `ip` 命令不管理 DNS！需修改配置文件：

#### **临时修改 DNS**

```
# 临时添加 DNS 服务器（重启失效）
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
```

#### **永久配置 DNS**

```
# 使用 NetworkManager (推荐)
sudo nmcli connection modify enp0s3 ipv4.dns "8.8.8.8 8.8.4.4"
sudo nmcli connection up enp0s3

# 或手动编辑连接配置文件
sudo vi /etc/NetworkManager/system-connections/enp0s3.nmconnection
# 在 [ipv4] 部分添加：
# dns=8.8.8.8;8.8.4.4;
# dns-search=example.com;
```

------

### **6. 接口状态控制**

```
# 启用接口
sudo ip link set enp0s3 up

# 禁用接口
sudo ip link set enp0s3 down

# 修改 MTU
sudo ip link set mtu 1500 dev enp0s3
```

------

### **7. 高级路由操作**

```
# 查看特定路由（如到 8.8.8.8）
ip route get 8.8.8.8

# 刷新 ARP 缓存
sudo ip neigh flush all

# 添加多路径路由 (ECMP)
sudo ip route add default scope global \
  nexthop via 192.168.1.1 dev eth0 weight 1 \
  nexthop via 192.168.2.1 dev eth1 weight 1
```

------

### **关键注意事项**

1. **临时性**
   `ip` 命令的配置**重启后失效**！永久配置需：

   - 使用 `nmcli`/`nmtui`（NetworkManager）
   - 编辑 `/etc/sysconfig/network-scripts/` 下的配置文件（传统方式）

2. **DNS 与 `ip` 命令无关**
   DNS 需通过 `/etc/resolv.conf` 或 NetworkManager 配置。

3. **服务依赖**
   修改配置后可能需要重启网络服务：

   ```
   sudo systemctl restart NetworkManager
   ```

------

### **推荐组合使用**

```
# 示例：临时配置 IP + 网关 + 测试
sudo ip address add 192.168.1.100/24 dev enp0s3
sudo ip link set enp0s3 up
sudo ip route add default via 192.168.1.1
ping -c 4 google.com
```

> 💡 生产环境建议使用 `nmcli` 或 Ansible 进行持久化配置。`ip` 命令更适合临时调试或脚本中使用。

