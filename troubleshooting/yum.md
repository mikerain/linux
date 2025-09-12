注册：

```
subscription-manager register qxu-redat
```

自动添加 repo

```
subscription-manager attach --auto
```

列出可附加到系统的订阅

```
subscription-manager list --available

```

显示远程repos

```
subscription-manager repos --list

```

```
subscription-manager list --installed
+-------------------------------------------+
    Installed Product Status
+-------------------------------------------+
Product Name:   Red Hat Enterprise Linux for x86_64
Product ID:     479
Version:        8.10
Arch:           x86_64
Status:         Subscribed
Status Details: 
Starts:         01/01/2022
Ends:           12/31/2026

```



```
subscription-manager repos --enable=rhel-8-for-x86_64-appstream-rpms
```



```
subscription-manager repos --list-enabled
+----------------------------------------------------------+
    Available Repositories in /etc/yum.repos.d/redhat.repo
+----------------------------------------------------------+
Repo ID:   rhel-8-for-x86_64-baseos-rpms
Repo Name: Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel8/$releasever/x86_64/baseos/os
Enabled:   1

Repo ID:   rhel-8-for-x86_64-appstream-rpms
Repo Name: Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel8/$releasever/x86_64/appstream/os
Enabled:   1

```



相关日志：

| 日志文件                   | 记录重点               | 适用场景                        |
| -------------------------- | ---------------------- | ------------------------------- |
| `/var/log/dnf.log`         | 用户执行命令的总体流程 | 回溯 dnf 命令历史、总体过程分析 |
| `/var/log/dnf.librepo.log` | 仓库访问 & 下载过程    | 排查网络/仓库访问问题           |
| `/var/log/dnf.rpm.log`     | RPM 安装/卸载细节      | 排查安装脚本、文件冲突问题      |



查看命令来自来哪yum或rpm包

```
rpm -qf /usr/sbin/lvs
lvm2-2.03.14-15.el8_10.2.x86_64

```





case:

[How to rebuild RPM database on a Red Hat Enterprise Linux system?]
https://access.redhat.com/solutions/6903

[yum and rpm get a db5 error]
https://access.redhat.com/solutions/6930551



Last opt
[How to debug recurring rpm database corruption]
https://access.redhat.com/solutions/3330211





case: https://access.redhat.com/support/cases/#/case/04095995

在rhel8上安装了rhel7的rpm,引起的so文件缺失，



rpm -Va 验证系统中已安装的所有 RPM 包中的文件是否与安装时记录的元数据一致

`rpm -qf` 是 **RPM 包管理器**中的一个查询子命令，作用是

```
# rpm -Va &> /tmp/rpmva.txt    ######### This command will take some time
# egrep -v " c | d | g " /tmp/rpmva.txt  | awk '{print $NF}' | sort | uniq | xargs rpm -qf 2> /dev/null | sort | uniq &> /tmp/pkgs.txt
```



 yum reinstall  重新安装，

`package-cleanup --problems` 的用途是：

👉 **检查系统中已安装的软件包是否存在依赖关系问题**。

```
 yum reinstall redhat-rpm-config gcc glibc-devel sysstat fontconfig ntp dejavu-sans-fonts glibc-headers cpp libxcrypt-devel iftop

- Provide the output of the following command:
 
# package-cleanup --problems
```
