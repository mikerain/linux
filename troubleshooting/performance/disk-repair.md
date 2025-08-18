fix xfs disk issue

将/dev/vdb 从/data中umount,然后修复

```
lsblk
df -h 
mount
umount /data

xfs_repair -n /dev/vdb  #不修复
xfs_repair  /dev/vdb  #修复

mount /dev/vdb /mnt/aaa  #to check
```



当遇到 `bad superblock` 时，90% 的情况可通过 `xfs_repair -L` 解决，但会丢失最后一次挂载后的文件修改。生产环境务必先备份！

