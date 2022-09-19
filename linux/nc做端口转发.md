## 架构图
server(10.0.12.15) <---agent(10.0.8.9)--->  data(10.0.8.8)

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
ncat --sh-exec "ncat 10.0.8.8 8098" -l 9000  --keep-open &
ncat --sh-exec "ncat 10.0.12.15 8099" -l 9001  --keep-open &

ncat --sh-exec "ncat -v -o 11.log 10.0.8.8 8098" -l 9000 --keep-open -o 1.log &
ncat --sh-exec "ncat -v -o 22.log 10.0.12.15 8099" -l 9001 --keep-open -o 2.log &
```
然后在server上：
```
访问：10.0.8.9:9000 就是访问data:8098端口
```
然后在data上：
```
访问：10.0.8.9:9001 就是访问server:8099端口
```


## nc做中间人通话

目标：
```
server1 <---agent---> server2

server1和server2能交流信息。
```

在agent上开2个nc：
```
mkfifo hubpipe
nc -l -p 2222 0<hubpipe|nc -l -p 3333|tee hubpipe
```

在server1上：
```
nc agent_ip 2222
```

在server2上：
```
nc agent_ip 3333
```


## 参考资料
* [https://cloud.tencent.com/developer/article/1688152](https://cloud.tencent.com/developer/article/1688152)
* [https://onestraw.github.io/cybersecurity/netcat-advanced-usage/](https://onestraw.github.io/cybersecurity/netcat-advanced-usage/)
* [https://www.cnblogs.com/xtxk110/p/14678178.html](https://www.cnblogs.com/xtxk110/p/14678178.html)
