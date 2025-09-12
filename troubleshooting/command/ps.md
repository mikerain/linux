按用户统计进程

```
ps --no-headers auxwwwm | awk '$2 == "-" { print $1 }' | sort | uniq -c | sort -n

  1 chrony
      1 dnsmasq
      1 nm-open+
      2 avahi
      2 dbus
      2 systemd+
      3 rtkit
      4 colord
      4 passim
      4 polkitd
      5 geoclue
     57 splunk
    327 root
   1254 qxu

```

