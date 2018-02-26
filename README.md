# 有趣的后台运维

啊，因为公司不大，没有专门的运维，所以很多时候都是自己去维护服务器。      
所以记录一下...    


## 服务访问不了或异常

### 分支1：服务的其它页面（功能）正常，或者同一页面的不同数据正常
这种一般是一个功能点有问题，在某些特定环境或者某些用户访问才会触发。     
解决方案：看日志。    
```
tail -10000 -f app.log | grep -10 ERROR
tail -10000 -f app.log | grep -A10 ERROR   //这个是ERROR往下的10行
tail -10000 -f app.log | grep -B10 ERROR   //这个是ERROR往上的10行
tail -10000 -f app.log | grep -C10 ERROR   //这个是ERROR上下的10行 == grep - 10 ERROR

cat app.log | grep -10 ERROR
```


### 分支2：SSH登录下服务器，观察打命令挺慢的
这种情况说明整个服务器是很慢很卡的，先打以下命令，然后根据：CPU、内存、硬盘等几个维度去分析   


#### 步骤2.1, 看看服务器的整体使用情况    
```
[root@iZ94won0vbvZ ~]# top
top - 18:48:56 up 236 days,  3:52,  3 users,  load average: 0.00, 0.06, 0.11
Tasks: 331 total,   1 running, 330 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.1 us,  1.6 sy,  0.0 ni, 96.2 id,  0.0 wa,  0.0 hi,  0.1 si,  0.0 st
KiB Mem : 16268344 total,   287464 free,  7401892 used,  8578988 buff/cache
KiB Swap: 16383996 total, 15928976 free,   455020 used.  7576280 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                 
18792 root      20   0 2514152  60468   2760 S   3.0  0.4   2556:07 java                                                                                    
 2214 rabbitmq  20   0 2316144  44888   1320 S   2.3  0.3   1652:51 beam.smp                                                                                
23754 root      20   0  534352 108292   5392 S   2.3  0.7   1107:12 mongod                                                                                  
 1872 mysql     20   0 2846424 1.188g   6460 S   1.7  7.7   1857:17 mysqld                                                                                  
 2736 root      20   0 1018504  19324    756 S   1.3  0.1   3204:31 python                                                                                  
21974 root      20   0 1393336  60660    972 S   1.3  0.4   1675:58 uwsgi                                                                                   
  952 root      20   0 1049312  69424   5952 S   1.0  0.4  17:44.64 uwsgi                                                                                      
```
load average: 0.00, 0.06, 0.11，分别表示在最近1分钟、5分钟、15分钟的负载，1.0以下都算是正常，但是一些老手说最好别超过0.7    
%CPU: 看哪个服务消耗CPU最多      
%MEM: 看哪个服务消耗内存最多  


##### 步骤2.1.1 CPU高 -> MySQL进程 -> 是否在执行耗时操作
```
mysql> show processlist;
+----------+---------+----------------------+----------------------+---------+-------+----------+------------------+
| Id       | User    | Host                 | db                   | Command | Time  | State    | Info             |
+----------+---------+----------------------+----------------------+---------+-------+----------+------------------+
| 15490667 | seafile | localhost:45682      | ccnet-db             | Sleep   |    23 |          | NULL             |
| 15490668 | seafile | localhost:45684      | seafile-db           | Sleep   |     3 |          | NULL             |
| 15496875 | seafile | localhost:60158      | ccnet-db             | Sleep   |  1530 |          | NULL             |
| 15498263 | seafile | localhost:35184      | seafile-db           | Sleep   |   630 |          | NULL             |
```
Sleep状态不需要关心，因为是空闲连接，看那些Command=Query的项，我们关心这些项的Time（时间单位：秒），看看有没有耗时比较大的，如果是select SQL语句，那么果断杀之，比如我们要杀Id=15490667的连接：
```
kill 15490667
```
如果是update等SQL语句，那么就要小心，判断下该sql是不是重要的sql，假设kill后有没有事务回滚或者日志恢复之类的异常处理，或者您就乖乖等执行完毕吧。不过一般update不会耗时很长，都是30-50ms操作完毕。


##### 步骤2.1.2，CPU高 -> MySQL进程 -> 慢查询日志
先找到慢查询日志的位置：
```
[root@iZ94won0vbvZ ~]# cat /etc/my.cnf | grep slow
slow_query_log=on
slow_query_log_file=/var/lib/mysql/slow_query.log
```
或者：
```
mysql> show variables like 'slow_query%';
+---------------------+-------------------------------+
| Variable_name       | Value                         |
+---------------------+-------------------------------+
| slow_query_log      | ON                            |
| slow_query_log_file | /var/lib/mysql/slow_query.log |
+---------------------+-------------------------------+
2 rows in set (0.01 sec)
```
然后看有哪些sql是超过2秒的，该加索引加索引，该调整业务逻辑调整业务逻辑，个人感觉：如果实现一个需求特复杂，会不会是我们一开始没考虑好架构或者是不是应该和产品再深入讨论下。


##### 步骤2.1.3，CPU高|内存高 -> 某个不是业务程序的进程 -> 该进程是成熟的应用
假设我们用top发现了PID=952的进程CPU跑得飞起或者用内存较多：
```
[root@iZ94won0vbvZ ~]# ps -ef | grep 952
root       952   931  1 Feb23 ?        00:49:12 uwsgi --ini /data1/www/xxx/uwsgi.ini
root     11987  6025  0 18:03 pts/0    00:00:00 grep --color=auto 952
```
但是该进程又是非常成熟的应用，比如nginx，比如mysql，比如supervisor，比如uwsgi，那么我们要去看些官网、论坛看看是不是有什么补丁或别人遇到问题讨论的帖子，理论上谷歌、Stack Overflow能出来不少解决方案。


##### 步骤2.1.4, CPU高|内存高 -> 某个不是业务程序的进程 -> 该进程是程序小哥哥写的
这个过问一下，很可能写了傻逼代码，比如死循环什么的。
优化之。


##### 步骤2.1.5，CPU高|内存高 -> 某个不是业务程序的进程 -> 不认识这个进程
那么就要提高警惕了，会不会是攻击或病毒，去谷歌、SO扫荡一波看看。


##### 步骤2.1.6，CPU高|内存高 -> 某个业务程序的进程 -> JAVA
额，大部分后台程序是java，我们就拿java来看吧，：
```
1、是否频繁full GC，如果我们给的内存太小，那么java会不断触发垃圾回收，增加CPU消耗；
2、jmap查看内存数据。
3、jstat
4、jmx
5、jhat
```
分析出来后丢给相应的开发小哥哥去处理...

##### 步骤2.1.7, CPU高|内存高 -> 某个业务程序的进程 -> Python
如果只是单个小模块觉得有问题，可以用自定义的计算时间耗时装饰器去修饰你想要的func()，然后看时间如何；如果是内存觉得有问题，可以看看是不是有大list出现，或者遍历大数，改成迭代器比较好...   
python基本上不用去关心GC，但是如果真的有内存疑惑，可以用memory_profiler去查看下：   
```
[root@iZ94won0vbvZ ~]# cat 1.py 
# coding=utf-8

@profile
def my_func():
    a = [1] * (10 ** 6)
    b = [2] * (2 * 10 ** 7)
    del b
    return a

if __name__ == '__main__':
    my_func()
    
[root@iZ94won0vbvZ ~]# python -m memory_profiler 1.py 
Filename: 1.py

Line #    Mem usage    Increment   Line Contents
================================================
     8   13.199 MiB   13.199 MiB   @profile
     9                             def my_func():
    10   20.832 MiB    7.633 MiB       a = [1] * (10 ** 6)
    11  173.422 MiB  152.590 MiB       b = [2] * (2 * 10 ** 7)
    12   20.832 MiB -152.590 MiB       del b
    13   20.832 MiB    0.000 MiB       return a
```
有些时候觉得不可能用这么多内存，可能是因为GC失败，那么就要考虑是否要搞弱引用了。
```
import gc
print gc.grabage
```
会打印一个列表，里面放着既不可达又引用计数不为0的对象。比如我们有个环A->B->C->A，分别删除A，B，C，那么
```
A=B=C=None
```
可是因为有C指向，A的引用计数=1，同理，B的引用计数=1，C的引用计数=1，都不为0，那么就会出现在gc.grabage列表中，如果这个列表一直得不到清理，就会耗费越来越多的内存，可怕。


##### 步骤2.1.8，负载高 -> vmstat
就是说我们看到load average这个参数比较高，本人曾经买过一个服务器，因为比较穷买了个low一点，装了一个mysql+nginx后，随便干点什么负载就达到50多，是的，是50多。。这辈子都没见过这么高的。           
我们主要是关心wa这个参数，表示等待的任务数，如果很高，说明系统调度不过来了呢。
```
[root@iZ94won0vbvZ ~]#     vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0 454956 875176 402472 7945192    0    0     6   263    0    0  4  2 94  1  0
 0  0 454956 875028 402472 7945192    0    0     0     0 1858 3442  1  1 98  0  0
 0  0 454956 875028 402472 7945192    0    0     0   108 1982 4083  1  1 97  0  0
 0  0 454956 874904 402472 7945192    0    0     0    24 1947 3602  1  1 98  0  0
```


#### 步骤2.2，看看服务器的内存使用情况，free -h
主要是看可用内存还剩下几多...还有7个G问题不大...   
```
[root@iZ94won0vbvZ ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:            15G        7.1G        270M        930M        8.2G        7.2G
Swap:           15G        444M         15G
```


#### 步骤2.3，看看服务器的硬盘使用情况，df -h
可以看到这些Use%都很低，问题不大...
```
[root@iZ94won0vbvZ ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1       493G  111G  357G  24% /
devtmpfs        7.8G     0  7.8G   0% /dev
tmpfs           7.8G   24K  7.8G   1% /dev/shm
tmpfs           7.8G  785M  7.0G  10% /run
tmpfs           7.8G     0  7.8G   0% /sys/fs/cgroup
tmpfs           1.6G     0  1.6G   0% /run/user/0
```
如果想单独看某个文件夹大小：
```
[root@iZ9458z0ss9Z ~]# cd /data/software/docker/
[root@iZ9458z0ss9Z docker]# du -h --max-depth=1 
318M    ./data
17M     ./bin
335M    .
```

#### 步骤2.4，看看服务器的访问情况
```
[root@iZ94won0vbvZ ~]# awk '{a[$1]+=1;}END{for(i in a){print a[i] " " i;}}' /etc/nginx/logs/access.log |sort -gr | head -10
407425 120.76.188.16
80618 119.137.53.54
66496 119.137.52.83
63242 119.137.54.122
62315 119.137.55.145
60038 119.137.52.109
54994 119.137.52.245
49317 119.137.53.216
49221 119.137.54.229
46162 119.137.54.219
```
如果有陌生ip地址访问，那么可以用firewall-cmd禁用之。
```
firewall-cmd --permanent --add-rich-rule='rule family=ipv4 source address="123.44.55.66" drop'
```
