enable password login:

```
setenforce 0

vi /etc/ssh/sshd_config
PasswordAuthentication yes
#PasswordAuthentication no

systemctl restart sshd
```

