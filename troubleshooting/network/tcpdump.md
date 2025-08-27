**使用 `tcpdump` 抓包排查网络问题的常见示例**。我会分成几个典型场景，并给出对应命令。

------

## 1. 抓取某个网卡的所有流量

```
tcpdump -i eth0
```

- `-i eth0`：指定网卡。
- 默认只显示前 68 字节的内容和报文摘要。

------

## 2. 抓取指定主机的流量

```
tcpdump -i eth0 host 192.168.1.100
```

- 只显示与 `192.168.1.100` 有关的进出流量。

------

## 3. 抓取指定端口的流量

```
tcpdump -i eth0 port 80
```

- 抓取 HTTP 流量（80端口）。

或者单向：

```
tcpdump -i eth0 dst port 80
tcpdump -i eth0 src port 80
```

------

## 4. 抓取指定协议的流量

```
tcpdump -i eth0 tcp
tcpdump -i eth0 udp
tcpdump -i eth0 icmp
```

------

## 5. 保存抓包文件，便于 Wireshark 分析

```
tcpdump -i eth0 -w output.pcap
```

- 保存为 `pcap` 文件，可以用 Wireshark 打开。

- 配合 `-s 0` 抓取完整报文：

  ```
  tcpdump -i eth0 -s 0 -w output.pcap
  ```

------

## 6. 常用排查场景示例

### (1) 查看某端口是否有连接请求

```
tcpdump -i eth0 tcp port 3306
```

- 例如排查 MySQL 是否有客户端访问。

### (2) 查看是否有丢包 / 重传

```
tcpdump -i eth0 tcp port 443 -vvv | grep retransmission
```

- 可以初步发现是否有 TCP 重传。

### (3) 检查 DNS 请求是否发出 & 有无返回

```
tcpdump -i eth0 port 53
```

- 如果看到 `Standard query` 有发出，但没有 `Standard query response`，说明可能 DNS 没有返回。

### (4) 检查与某个远程服务的连通性

```
tcpdump -i eth0 host 8.8.8.8 and port 443
```

- 可以确认是否成功建立三次握手：
  - `S` → SYN
  - `S.` → SYN+ACK
  - `.` → ACK

------

## 7. 排查技巧

- **只看头部，不抓正文**：`tcpdump -i eth0 -n -vv`

- **显示数据内容**：

  ```
  tcpdump -i eth0 -X port 80
  ```

  （`-X` 会显示十六进制和 ASCII 内容）

- **限制抓包数量**：

  ```
  tcpdump -i eth0 -c 100
  ```

  （只抓 100 个包，避免刷屏）

------

✅ 总结：

- **定位通信是否发生**：`host` + `port`
- **定位协议是否正确**：`tcp/udp/icmp`
- **定位丢包重传问题**：抓取 TCP 包看握手与重传
- **进一步分析**：导出 `.pcap` 用 Wireshark 查看