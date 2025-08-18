ip禁ping

对本机ping不生效，需要从其他host ping本地ip进行测试

```
systemctl start firewalld
systemctl enable firewalld

firewall-cmd --query-icmp-block=echo-reply --permanent 
firewall-cmd --permanent --set-target=DROP --permanent  #主要这个起作用
firewall-cmd --reload

```

 查询

```
firewall-cmd --list-icmp-blocks
echo-reply
```



取消以上操作

```
firewall-cmd --remove-icmp-block=echo-reply --permanent 
firewall-cmd --permanent --set-target=default --permanent
firewall-cmd --reload
firewall-cmd --list-icmp-blocks
systemctl stop firewalld
systemctl disable firewalld
```

