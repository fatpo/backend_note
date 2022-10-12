作用：
```
1、查看本机的网络 IP
2、查看网卡信息
3、查看网关 IP
```
演示，可以看到我本机 IP 是`10.0.8.8`和` 127.0.0.1 `：
```
[root@rddata web]# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.8.8  netmask 255.255.252.0  broadcast 10.0.11.255
        inet6 fe80::5054:ff:fe7a:5f4f  prefixlen 64  scopeid 0x20<link>
        ether 52:54:00:7a:5f:4f  txqueuelen 1000  (Ethernet)
        RX packets 52101434  bytes 33282049666 (30.9 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 49291398  bytes 3775908372 (3.5 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 1529885  bytes 222196587 (211.9 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1529885  bytes 222196587 (211.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
