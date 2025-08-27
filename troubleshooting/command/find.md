

## 1. 使用 `du` 查找大目录和文件

```
# 查看当前目录下最大的 20 个子目录/文件
du -ahx / | sort -rh | head -20
```

- `-a`：显示文件和目录
- `-h`：人类可读（KB/MB/GB）
- `-x`：限制在当前挂载点（避免跨文件系统）
- `sort -rh`：按大小倒序排序
- `head -20`：取前 20 个

如果只想看目录占用情况：

```
du -shx /* | sort -rh | head -20
```

------

## 2. 使用 `find` 查找大文件

```
# 查找大于 500MB 的文件
find / -xdev -type f -size +500M -exec ls -lh {} \; | sort -k5 -rh | head -20
```

- `-xdev`：避免跨文件系统
- `-type f`：只查找文件
- `-size +500M`：大于 500MB
- `ls -lh`：显示文件大小