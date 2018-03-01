* [服务访问不了或异常慢卡](#服务访问不了或异常慢卡)
* [正在运行的业务进程CPU调优](#正在运行的业务进程CPU调优)
  * [Java调优](#java调优)
  * [Python调优](#python调优)
* [Mysql日常优化](#mysql日常优化)
   * [Mysql查看慢查询日志](#mysql查看慢查询日志)
   * [Mysql查看正在执行的sql](#mysql查看正在执行的sql)
* [Nginx查看访问最频繁的IP并禁止某个异常IP](#nginx查看访问最频繁的ip并禁止某个异常ip)




# 服务访问不了或异常慢卡
![Alt text](https://github.com/emaste-r/backend_note/blob/master/%E6%9C%8D%E5%8A%A1%E6%8E%92%E6%9F%A5.png)


# 正在运行的业务进程CPU调优

## Java调优
先找到CPU异常的PID：
```
[root@iZ9458z0ss9Z ~]# top -c
```
然后查看该进程下的线程：
```
[root@iZ9458z0ss9Z ~]# top -Hp YOUR_PID
```
然后查看异常的线程：
```
[root@iZ9458z0ss9Z ~]# jstack YOUR_THREAD_PID
```
如此，便能查看该线程到底在执行到哪一段代码会导致CPU异常：
```
[root@iZ94won0vbvZ ~]# printf '%x\n' 15589
3ce5
[root@iZ94won0vbvZ ~]# jstack 15589 | grep "3ce" -C 5

"qtp540159270-15" #15 prio=5 os_prio=0 tid=0x00007f312878e000 nid=0x3cfd waiting on condition [0x00007f311115f000]
   java.lang.Thread.State: TIMED_WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000000e002b348> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.parkNanos(LockSupport.java:215)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.awaitNanos(AbstractQueuedSynchronizer.java:2078)
--
        at org.eclipse.jetty.util.thread.QueuedThreadPool.idleJobPoll(QueuedThreadPool.java:531)
        at org.eclipse.jetty.util.thread.QueuedThreadPool.access$700(QueuedThreadPool.java:47)
        at org.eclipse.jetty.util.thread.QueuedThreadPool$3.run(QueuedThreadPool.java:590)
        at java.lang.Thread.run(Thread.java:745)
```

可能会出现如下错误：
```
Unable to open socket file: target process not responding or HotSpot VM not loaded
```
原因：
```
jstack找不到pid文件。
```
解决方案：
```
1、要么是因为pid文件放在/tmp文件夹下，然而tmpwatch策略导致系统定时清理掉/tmp文件夹下的长时间无访问的文件...
   只能重启才能重新生成pid；
2、也有可能pid文件被自定义设置到别处，那么jstack /data/you/path/your.pid即可。
```

## Python调优

本人版本：
```
centos7+python2.7.5   
```
安装gdb：
```
[root@iZ9458z0ss9Z ~]# sudo yum install gdb
```
出现如下信息说明安装成功：
```
[root@ouyang ~]# gdb
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-100.el7_4.1
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
(gdb) 
```
安装python包（才能使用py-list、py-bt等操作）：
```
wget http://debuginfo.centos.org/6/x86_64/python27-python-debuginfo-2.7.5-10.el6.centos.alt.x86_64.rpm
rpm -ivh python27-python-debuginfo-2.7.5-10.el6.centos.alt.x86_64.rpm
```
出现如下信息说明python-debuginfo包安装成功：
```
[root@iZ94won0vbvZ ~]# gdb -p 31618 # 31618 是某个python进程的PID
 
(gdb) py
py-bt               py-down             py-list             py-locals           py-print            py-up               python              python-interactive  
```
开始调试，查看当前线程运行的代码：
```
(gdb) py-list
 858                        # clear alarm so it doesn't fire while poll is waiting for
 859                        # events.
 860                        signal.setitimer(signal.ITIMER_REAL, 0, 0)
 861    
 862                    try:
>863                        event_pairs = self._impl.poll(poll_timeout)
 864                    except Exception as e:
 865                        # Depending on python version and IOLoop implementation,
 866                        # different exception types may be thrown and there are
 867                        # two ways EINTR might be signaled:
 868                        # * e.errno == errno.EINTR
(gdb) 
```

# Mysql日常优化

## Mysql查看慢查询日志
首先要判断mysql是否开启慢查询、慢查询日志存放位置：
```
[root@iZ9458z0ss9Z ~]#  cat /etc/my.cnf| grep slow           
slow_launch_time=2  # 表示如果建立线程花费了比这个值更长的时间,slow_launch_threads 计数器将增加
slow_query_log=on   # 开启 慢查询
slow_query_log_file=/data/log/your_slow_query.log  # log文件存放位置
```
万一没开启，怎么开启呢？
```
mysql> set global slow_query_log='ON'; 
mysql> set global slow_query_log_file='/usr/local/mysql/data/slow.log';
mysql> set global long_query_time=1;  # 超过一秒的查询会被记录
```
查看慢查询日志，主要关心参数：   
Query_time = 查询耗费多少秒 = 3.088468    
Rows_examined = 影响的行数 = 43817    
```
[root@iZ9458z0ss9Z ~]# tail -f /data/log/your_slow_query.log
# Time: 180301 16:17:20
# User@Host: root[root] @ iZ9458z0ss9Z [10.24.245.83]
# Thread_id: 9601563  Schema: miaoyan  QC_hit: No
# Query_time: 3.088468  Lock_time: 0.000084  Rows_sent: 1  Rows_examined: 43817
# Rows_affected: 0
SET timestamp=1519892240;
select count(id) as cnt from `your_table` where com_id = 1867 and ptype = 1;
```

## Mysql查看正在执行的sql
查看当前的sql执行情况：
```
mysql> show processlist;
+----------+---------+----------------------+----------------------+---------+-------+----------+------------------+
| Id       | User    | Host                 | db                   | Command | Time  | State    | Info             |
+----------+---------+----------------------+----------------------+---------+-------+----------+------------------+
| 15490667 | seafile | localhost:45682      | ccnet-db             | Sleep   |    23 |          | NULL             |
| 15490668 | seafile | localhost:45684      | seafile-db           | Sleep   |     3 |          | NULL             |
| 15496875 | seafile | localhost:60158      | ccnet-db             | Sleep   |  1530 |          | NULL             |
| 15498263 | seafile | localhost:35184      | seafile-db           | Sleep   |   630 |          | NULL             |
| 9604402  | root    | iZ9458z0ss9Z:22193   | your_db              | Query   |     1 | Sending data                                                                  | select count(id) as cnt from `your_table` where com_id = 1769 and ptype=1 |
```
Sleep状态的项不需要管，如果看到一个Query状态的sql，Time(秒)还很大，可能卡住了，个人感觉select的sql如果卡住了，可以先kill掉：
```
kill 9604402
```
再慢慢分析是否有索引：
```
explain select count(id) as cnt from `your_table` where com_id = 1769 and ptype=1;
```
如果是Update的sql，那就还是慢慢等执行完毕吧，一般不会耗费太多时间，除非你全表update。


# Nginx查看访问最频繁的IP并禁止某个异常IP

查看排名前10个访问最频繁的IP：
```
[root@iZ9458z0ss9Z ~]# awk '{a[$1]+=1;}END{for(i in a){print a[i] " " i;}}' /var/log/nginx/access.log |sort -gr | head -10                          
11929 123.44.55.66
9727 119.137.52.231
8132 111.18.73.48
2926 115.191.176.18
2407 183.198.212.108
2322 218.11.141.142
2257 183.225.60.104
2229 183.40.130.198
1990 115.207.217.131
1965 221.180.236.146
[root@iZ9458z0ss9Z ~]# 
```
假设我们感觉这个123.44.55.66太可疑了，如何用firewall禁用：
```
[root@iZ9458z0ss9Z ~]# firewall-cmd --permanent --add-rich-rule='rule family=ipv4 source address="123.44.55.66" drop'
```
如果未安装firewall-cmd，那么可以试试iptables:
```
[root@iZ9458z0ss9Z ~]# iptables -I INPUT -s 123.44.55.66 -j DROP
```
