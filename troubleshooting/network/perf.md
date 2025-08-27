网络性能问题

通过tcpdump和wireshark结合使用

tcpdump用来抓包

wireshark用来分析包



```
sar -n DEV 1 5

<pre>inux 4.18.0-553.el8_10.x86_64 (localhost.localdomain) 	08/26/2025 	_x86_64_	(2 CPU)

02:44:15 AM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
02:44:16 AM        lo 677792.00 677792.00 5904807.89 5904807.89      0.00      0.00      0.00      0.00
02:44:16 AM      eth0      1.00      0.00      0.05      0.00      0.00      0.00      0.00      0.00

02:44:16 AM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
02:44:17 AM        lo 678250.00 678250.00 5908919.96 5908919.96      0.00      0.00      0.00      0.00
02:44:17 AM      eth0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

02:44:17 AM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
02:44:18 AM        lo 675428.00 675428.00 5884247.88 5884247.88      0.00      0.00      0.00      0.00
02:44:18 AM      eth0      2.00      0.00      0.26      0.00      0.00      0.00      0.00      0.00
```

