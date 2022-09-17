## 架构图
server <---agent--->  data

## nc做转发
下载nc:
```
yum install nmap-ncat -y
```
原理：
```
监听本机 9876 端口，将数据转发到 192.168.172.131的 80 端口

ncat --sh-exec "ncat 192.168.172.131 80" -l 9876  --keep-open
```
在agent上：
```
ncat --sh-exec "ncat server_ip 8099" -l 9000  --keep-open &
ncat --sh-exec "ncat data_ip 8098" -l 9001  --keep-open &
```

## 参考资料
[https://cloud.tencent.com/developer/article/1688152](https://cloud.tencent.com/developer/article/1688152)
