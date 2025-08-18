### 2.1 查看虚机信息

```
virsh list             # 列出运行中的虚机
virsh list --all       # 列出所有虚机
virsh dominfo VM_NAME  # 查看指定虚机详细信息
virsh domuuid VM_NAME  # 获取虚机 UUID
virsh domstate VM_NAME # 查看虚机状态

virsh console <VM_NAME>
```



进入控制台后，可以像在串口终端一样直接操作虚机（前提是虚机里已启用串口终端）。

要退出控制台：

- 按 **Ctrl + ]** （不是 Ctrl+C）

### 2.2 启动/关闭/重启虚机

```
virsh start VM_NAME        # 启动
virsh shutdown VM_NAME     # 正常关机（ACPI 信号）
virsh destroy VM_NAME      # 强制关机（相当于拔电源）
virsh reboot VM_NAME       # 重启
```

### 2.3 创建虚机

#### 从 ISO 安装：

```
virt-install \
  --name testvm \
  --ram 2048 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/testvm.qcow2,size=20 \
  --cdrom /iso/CentOS-Stream.iso \
  --network network=default \
  --os-type linux \
  --os-variant centos8
```

#### 从镜像启动：

```
virt-install \
  --name testvm \
  --ram 2048 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/testvm.qcow2 \
  --import \
  --network network=default
```

### 2.4 磁盘操作

```
qemu-img create -f qcow2  /opt/vm/data.qcow2 10G                        # 创建 qcow2 磁盘,这时文件不是10G，只有192k

qemu-img info  /opt/vm/data.qcow2                                      # 查看磁盘信息
chown qemu:qemu  /opt/vm/data.qcow2

virsh attach-disk VM_NAME /path/to/data.qcow2 vdb --persistent  # 挂载磁盘
virsh detach-disk VM_NAME vdb --persistent                     # 卸载磁盘

如果attach-disk加到vm中的磁盘不能被正确的识别大小，分使用以下命令来挂载：
#virsh attach-disk myvm /opt/vm/data.qcow2 vdb --driver qemu --subdriver qcow2 --targetbus virtio --persistent


```



```
lsblk       # 查看是否识别到新磁盘

fdisk -l /dev/vdb  #可以识别正确的size
Disk /dev/vdb: 10 GiB, 10737418240 bytes, 20971520 sectors

格式化并挂载：

sudo mkfs.xfs /dev/vdb
sudo mkdir /data
sudo mount /dev/vdb /data
```



### 2.5 网络管理

```
virsh net-list                 # 查看虚拟网络
virsh net-start default        # 启动默认网络
virsh net-autostart default    # 设置开机自启
virsh attach-interface VM_NAME network default --model virtio --persistent # 添加网卡
```

### 2.6 快照管理

```
virsh snapshot-create-as VM_NAME snap1 "Before upgrade"   # 创建快照
virsh snapshot-list VM_NAME                              # 查看快照
virsh snapshot-revert VM_NAME snap1                      # 回滚快照
virsh snapshot-delete VM_NAME snap1                      # 删除快照
```

### 2.7 迁移与导出

```
virsh dumpxml VM_NAME > vm.xml                           # 导出虚机定义
virsh define vm.xml                                      # 导入虚机定义
virsh migrate --live VM_NAME qemu+ssh://target/system    # 实时迁移
virsh domblklist VM_NAME                                 # 查看磁盘文件路径
```



## **看虚机磁盘文件路径**

```
virsh domblklist VM_NAME
```

示例：

```
$ virsh domblklist testvm
Target   Source
------------------------------------------------
vda      /var/lib/libvirt/images/testvm.qcow2
hdc      /var/lib/libvirt/images/CentOS-Stream.iso
```

- **Target** → 虚机里显示的磁盘名称（如 `/dev/vda`）
- **Source** → 宿主机上的磁盘文件路径

------

## **2. 查看磁盘文件详细信息**

```
virsh domblkinfo VM_NAME vda
```

