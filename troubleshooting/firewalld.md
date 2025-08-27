

为某服务添加firewalld的流量通过

```
firewall-cmd --add-service=cockpit --permanent
firewall-cmd --reload
```



```
firewall-cmd --list-all-zones



firewall-cmd --list-all-zones --permanent
```



firewall-cmd --runtime-to-permanent  	命令可将运⾏时规则转换为永久规则。
