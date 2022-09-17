## nload
centos:
```
yum install -y epel-release && yum install -y nload
```
运行：
```
nload
```
会出来一个小界面，还是很直观的。

![image](https://user-images.githubusercontent.com/6395350/190845417-7656400b-fd0c-4aa4-bd35-876859bef525.png)


## dstat
安装:
```
yum install -y dstat
```
运行效果：
```
[root@server ~]# dstat 
You did not select any stats, using -cdngy by default.
----total-cpu-usage---- -dsk/total- -net/total- ---paging-- ---system--
usr sys idl wai hiq siq| read  writ| recv  send|  in   out | int   csw 
  0   0  99   0   0   0|  42k   67k|   0     0 |   0     0 | 618  1139 
  1   1  99   0   0   0|   0     0 | 648B 2136B|   0     0 | 654  1133 
  0   1 100   0   0   0|   0     0 |  96B  896B|   0     0 | 546  1012 
  1   0 100   0   0   0|   0     0 |  54B  362B|   0     0 | 495   942 
  1   1  99   0   0   1|   0   100k|1035B 1700B|   0     0 | 719  1247 
  1   1  99   0   0   0|   0  4096B|1200B 3547B|   0     0 | 666  1138 
  1   0  99   0   0   0|   0     0 | 108B  416B|   0     0 | 638  1201 
  0   0  99   0   0   0|   0     0 |  96B 1252B|   0     0 | 594  1150 
  1   0 100   0   0   0|   0     0 |  96B  404B|   0     0 | 584  1083 
  0   0 100   0   0   0|   0     0 |  96B  404B|   0     0 | 519   974 
  1   0 100   0   0   0|   0    40k|  54B   42B|   0     0 | 524   978 
  1   1  99   0   0   0|   0     0 | 270B  948B|   0     0 | 655  1172 
  0   0  99   0   0   0|   0     0 | 528B 1068B|   0     0 | 568  1025 
  0   0 100   0   0   0|   0     0 |  96B  108B|   0     0 | 520   965 
  1   0  99   0   0   0|   0     0 | 892B 1870B|   0     0 | 628  1108 
  1   1  99   0   0   0|   0  4096B|1200B 3231B|   0     0 | 659  1138 
  1   0  99   1   0   0|   0    40k|  54B   54B|   0     0 | 682  1354 
  1   1  99   0   0   0|   0     0 | 138B  766B|   0     0 | 666  1218 
  0   0 100   0   0   0|   0     0 |  96B  404B|   0     0 | 568  1064 
  1   0 100   0   0   0|   0     0 |  54B   42B|   0     0 | 515   976 
  0   0  99   0   0   0|   0  4096B| 754B 1490B|   0     0 | 649  1162 
  2   2  97   0   0   0|   0   156k| 648B 1270B|   0     0 | 868  1609
```


## sar

sar 是`System Activity Report`的缩写，是一款全面的Linux系统运行状态统计和性能分析工具，可从磁盘IO、CPU负载、内存使用等多个维度对系统活动进行报告。

sar 命令来自 sysstat 包，可使用这个命令安装：
```
yum install -y sysstat
```

sar -n TCP 1 10可查看接下来10秒内的tcp数据：
```
[root@server ~]# sar -n TCP 1 10
Linux 3.10.0-1160.71.1.el7.x86_64 (rdserver) 	09/17/2022 	_x86_64_	(2 CPU)

03:10:14 PM  active/s passive/s    iseg/s    oseg/s
03:10:15 PM      1.00      0.00      9.00      8.00
03:10:16 PM      2.00      0.00     14.00     15.00
03:10:17 PM      0.00      0.00      1.00      1.00
03:10:18 PM      0.00      0.00      1.00      1.00
03:10:19 PM      0.00      0.00      3.00      3.00
03:10:20 PM      0.00      0.00      2.00      3.00
03:10:21 PM      0.00      0.00      1.00      1.00
03:10:22 PM      1.00      0.00      7.00      7.00
03:10:23 PM      0.00      0.00      1.00      1.00
03:10:24 PM      0.00      0.00      1.00      1.00
Average:         0.40      0.00      4.00      4.10

```

## ifto
安装：
```
yum install -y epel-release && yum install -y ifto
```
运行：
```
iftop -nN -i eth0
```
这个能看到和具体的IP连接的数据流量，还是很棒的。
![image](https://user-images.githubusercontent.com/6395350/190845374-32511eff-d115-4ae9-93cd-45ac864a260a.png)

