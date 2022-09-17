## 背景
centos7以上都采用 `firewall-cmd` 来替代`iptables` 管理白名单

### 富规则（rich rules的背景）
在某些情况下，我们可能需要创建更复杂的规则，而不仅仅是允许区域中的某些端口或服务。

例如，我们可能想要创建一条规则来阻止来自特定机器的某种类型的流量。

这就是rich规则的意义所在。

规则基本上由两部分组成：在第一部分中，我们指定要应用规则必须满足的条件，在第二部分中，指定要执行的操作:accept、drop或reject


### 富规则的语法

--add-rich-rule选项，将该规则描述为其参数。规则以rule关键字开头：
```
family:我们指定该规则仅应用于IPv4数据包：如果未提供此关键字，则该规则将同时应用于IPv4和IPv6。
source address:我们提供数据包必须具有的源地址，才能使用源地址触发规则。
service，我们指定了规则的服务类型，在本例中为ssh。
reject/drop/accept，我们提供了数据包与规则匹配时要执行的操作，在本例中为reject.
```


## 打开防火墙 & 开机自启动
```
systemctl start firewalld
systemctl enable firewalld
```
## 查看状态
```
systemctl status firewalld
```
效果：
```
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: active (running) since Sat 2022-09-17 11:56:30 CST; 45s ago
     Docs: man:firewalld(1)
 Main PID: 21914 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─21914 /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid

Sep 17 11:56:30 server systemd[1]: Starting firewalld - dynamic firewall daemon...
Sep 17 11:56:30 server systemd[1]: Started firewalld - dynamic firewall daemon.
Sep 17 11:56:30 server firewalld[21914]: WARNING: AllowZoneDrifting is enabled. This is considered an insecure configuration option. It will be removed in a future release. Please consider disabling it now.
```

## 添加一个ip:port的访问权限
```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.0.8.9" port protocol="tcp" port="2000" accept"

firewall-cmd --reload
```
## 删除一个ip:port的访问权限
```
firewall-cmd --permanent --remove-rich-rule="rule family="ipv4" source address="10.0.12.16" port protocol="tcp" port="2000" accept"

firewall-cmd --reload
```

## 查看当前规则
```
firewall-cmd --zone=public --list-rich-rules
```
效果：
```
rule family="ipv4" source address="10.0.8.9" port port="2000" protocol="tcp" accept
```
查看更多的规则：
```
[root@server ~]# firewall-cmd --zone=public --list-all
public
  target: default
  icmp-block-inversion: no
  interfaces: 
  sources: 
  services: dhcpv6-client ssh
  ports: 
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
	rule family="ipv4" source address="10.0.8.9" port port="2000" protocol="tcp" accept
```

## 重新载入一下防火墙设置，使设置生效
```
firewall-cmd --reload
```

## 查询打开的端口
```
firewall-cmd --zone=public --list-ports
```


## 查询资料
[https://www.jianshu.com/p/20b8ec56b9f8](https://www.jianshu.com/p/20b8ec56b9f8)
