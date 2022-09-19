## 架构图
server(10.0.12.15) <---agent(10.0.8.9)--->  data(10.0.8.8)

## portmap做转发
下载portmap(就是 linux 版本的lcx):
```
http://www.vuln.cn/wp-content/uploads/2016/06/lcx_vuln.cn_.zip
unzip lcx_vuln.cn_.zip
chmod +x portmap
```
在agent上：
```
 ./portmap -m 1 -p1 9000 -h2 10.0.8.8 -p2 8098 &
 ./portmap -m 1 -p1 9001 -h2 10.0.12.15 -p2 8099 &
```
然后在server上：
```
访问：10.0.8.9:9000 就是访问data:8098端口
```
然后在data上：
```
访问：10.0.8.9:9001 就是访问server:8099端口
```

## 参考资料
* [Linux下常用的端口转发工具](https://www.cnblogs.com/xtxk110/p/14678178.html)
