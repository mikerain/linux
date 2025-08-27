

模拟网络流量

```
stress-ng --sock 2 --timeout 600s ＆
```



查看网络流量，注意下面本地的地址和端口

```
netstat -tunp|more
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       
PID/Program name    
tcp        0      0 127.0.0.1:5000          127.0.0.1:40084         TIME_WAIT   
-                   
tcp        0      0 127.0.0.1:5001          127.0.0.1:45252         TIME_WAIT   
-                   
tcp        0      0 127.0.0.1:5000          127.0.0.1:41146         TIME_WAIT   
-                   
tcp        0      0 127.0.0.1:5001          127.0.0.1:55650         TIME_WAIT   
-                   
tcp        0      0 127.0.0.1:5000          127.0.0.1:38386         TIME_WAIT   
-                   
tcp        0      0 127.0.0.1:5001          127.0.0.1:50506         TIME_WAIT   
-                   

```



然后查看对应的端口和应用的关系

```
 ss -tulnp
Netid State  Recv-Q Send-Q Local Address:Port Peer Address:PortProcess                                                  
udp   UNCONN 0      0            0.0.0.0:111       0.0.0.0:*    users:(("rpcbind",pid=653,fd=5),("systemd",pid=1,fd=97))
udp   UNCONN 0      0          127.0.0.1:323       0.0.0.0:*    users:(("chronyd",pid=701,fd=5))                        
udp   UNCONN 0      0               [::]:111          [::]:*    users:(("rpcbind",pid=653,fd=7),("systemd",pid=1,fd=99))
udp   UNCONN 0      0              [::1]:323          [::]:*    users:(("chronyd",pid=701,fd=6))                        
tcp   LISTEN 0      10           0.0.0.0:5000      0.0.0.0:*    users:(("stress-ng-sock",pid=4436,fd=4))                
tcp   LISTEN 1      10           0.0.0.0:5001      0.0.0.0:*    users:(("stress-ng-sock",pid=4437,fd=4))                
tcp   LISTEN 0      128          0.0.0.0:111       0.0.0.0:*    users:(("rpcbind",pid=653,fd=4),("systemd",pid=1,fd=96))
tcp   LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=1038,fd=3))                          
tcp   LISTEN 0      128             [::]:111          [::]:*    users:(("rpcbind",pid=653,fd=6),("systemd",pid=1,fd=98))
tcp   LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=1038,fd=4))          
```



查看流量在哪个网卡上：



```
cat /proc/net/dev
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
    lo: 3448589197832 386530843    0    0    0     0          0         0 3448589197832 386530843    0    0    0     0       0      
    0
  eth0: 710655671  467018    0 38075    0     0          0         0 18787091  219102    0    0    0     0       0          0

```



```
netstat -i
Kernel Interface table
Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
eth0             1500   414044      0  35352 0        192365      0      0      0 BMRU
lo              65536 241285788      0      0 0      241285788      0      0      0 LRU
```

