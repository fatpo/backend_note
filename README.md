* [服务访问不了或异常慢卡](#服务访问不了或异常慢卡)  
* [正在运行的业务进程CPU调优](#服务访问不了或异常慢卡)
   * [Java调优](#java调优)
   * [Python调优](#python调优)
* [Mysql日常优化](#mysql日常优化)
   * [Mysql查看慢查询日志](#mysql查看慢查询日志)
   * [Mysql查看正在执行的sql](#mysql查看正在执行的sql)
* [Nginx查看访问最频繁的IP并禁止某个异常IP](#nginx查看访问最频繁的ip并禁止某个异常ip)
   * [firewall-cmd](#firewall-cmd)
   * [iptables](#iptables)
* [监控硬盘并邮件报警](#监控硬盘并邮件报警)



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

## firewall-cmd
假设我们感觉这个123.44.55.66太可疑了，如何用firewall禁用：
先查看firewall是否开启：
```
[root@iZ9458z0ss9Z ~]# firewall-cmd --state
running
```
禁用123.44.55.66：
```
[root@iZ9458z0ss9Z ~]# firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='123.44.55.66' reject"
success
```
重启生效:
```
[root@iZ9458z0ss9Z ~]# firewall-cmd --reload
success
[root@iZ9458z0ss9Z ~]# iptables -L | grep '123'
REJECT     all  --  123.44.55.66         anywhere             reject-with icmp-port-unreachable
```
解封IP，把--add-rich-rule改成--remove-rich-rule:
```
[root@iZ9458z0ss9Z ~]# firewall-cmd --permanent --remove-rich-rule="rule family='ipv4' source address='123.44.55.66' reject"
```

## iptables
如果未安装firewall-cmd，那么可以试试iptables，先看iptables服务跑起来没：
```
[root@iZ94won0vbvZ ~]# service iptables status
```
如果没有跑起来：
```
重启后生效
    开启： chkconfig iptables on
    关闭： chkconfig iptables off
即时生效，重启后失效
    开启： service iptables start
    关闭： service iptables stop
```
添加拒绝ip的规则：
```
[root@iZ9458z0ss9Z ~]# cp /etc/sysconfig/iptables-config  /var/tmp   //先保存原先配置
[root@iZ9458z0ss9Z ~]# iptables -I INPUT -s 123.44.55.66 -j DROP //添加拒绝的ip
[root@iZ9458z0ss9Z ~]# service iptables save  //保存配置
[root@iZ9458z0ss9Z ~]# service iptables restart //重启防火墙
```
查看下添加成功否：
```
[root@iZ94won0vbvZ ~]# iptables -L  //查看iptables
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
DROP       all  --  123.44.55.66         anywhere            
ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
```
如果发现是个误会，想解封123.44.55.66：
```
[root@iZ94won0vbvZ ~]# iptables -D INPUT -s 123.44.55.66 -j DROP
[root@iZ94won0vbvZ ~]# iptables -L | grep 123.44.55   //发现找不到和123.44.55.66相关的规则了，解封成功！
[root@iZ94won0vbvZ ~]# 
```

# 监控硬盘并邮件报警

我们查看硬盘使用率 df -h：
```
[root@iZ9458z0ss9Z script]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        40G   14G   24G  36% /
devtmpfs        3.9G     0  3.9G   0% /dev
tmpfs           3.9G   28K  3.9G   1% /dev/shm
tmpfs           3.9G  408M  3.5G  11% /run
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/vdb        197G  124G   64G  66% /data
tmpfs           783M     0  783M   0% /run/user/0
```

如果从未配置过mail：
```
[root@001 ~]# vim /etc/mail.rc #添加如下内容
set from=xxxx@126.com  # from：对方收到邮件时显示的发件人
set smtp=smtp.126.com  # smtp：指定第三方发邮件的smtp服务器地址
set smtp-auth-user=xx@126.com  # set smtp-auth-user：第三方发邮件的用户名
set smtp-auth-password=xxx  # set smtp-auth-password：用户名对应的密码,有些邮箱填的是授权码
set smtp-auth=login # smtp-auth：SMTP的认证方式，默认是login，也可以改成CRAM-MD5或PLAIN方式
```
测试下：
```
[root@iZ9458z0ss9Z script]# echo "测试邮件" | mail -s "测试主题" xihuanjianguo@163.com
```

写发送邮件的脚本：
```
[root@iZ9458z0ss9Z script]# cat sendmail.sh 
#!/bin/bash
# by kin
source /etc/profile
content=$1
address=$2
echo ${content} | mail -s ${content} ${address}
```

写监控硬盘脚本：
```
[root@iZ9458z0ss9Z ~]# cat /data/your_script_path/hdd_alarm.sh 
#!/bin/sh
# by kin
source /etc/profile;
runpath='/data/your_script_path'

# 定义send函数来调用我们的sendmail.sh脚本
send()
{
  ./sendmail.sh $1 your_email_1@163.com
  ./sendmail.sh $1 your_email_2@163.com
}

# 如果不存在hdd_alarm文件夹则创建之
if [ ! -d  $runpath/hdd_alarm ]
then
        mkdir $runpath/hdd_alarm
fi

# 获取本机的IP地址
Host_IP=`ifconfig |grep "inet "|grep -v 127.0.0.1|grep -v 'inet 10.'|grep -v '192.168'|awk '{print $2}'`


# 获取df硬盘容量参数的后两列使用率a和路径b，遍历a和b
df -h | awk '{print $(NF-1),$NF}'|awk '{if($NF!=$1){print $0}}'|sed 's/\%//g'|sed '1d'|while read a b;
do

# 如果a和b有一个是空字符串则跳过
if [ -z $a ] || [ -z $b ] 
then      
        :
else
        # 从日志中获取上一次报警的使用率
        old_used=`cat $runpath/hdd_alarm/hdd_tmp.log 2>/dev/null|grep "$b$"|awk '{print $(NF-1)}'|sed 's/\%//g'`
        
        # 如果使用率不超过80 或者 相比上一次没有增加，则跳过
        if [ ! -z $old_used ] && [ $old_used -eq $a ] &&  [ $a -le 80 ]
        then
                continue;
        fi
        
        # 如果使用率确实>=80，则调用send()发送邮件...
        if  [ $a -gt 80  ] ; 
        then
                msg="Hdd_alarm:$Host_IP-$b-Reach-$a-precent"
                echo "$msg" #>>./Hdd_alarm.log
                send "$msg"
                
                # 如果调用send()的返回值不是0，说明有错误
                if [ $? -ne 0 ]; 
                then
                        echo -e "############Send Fail#############"
                fi
        fi


fi
done

# 把df信息存入到日志中，方便下一次对比
df -h >$runpath/hdd_alarm/hdd_tmp.log

```
放到定时任务，每15分钟跑一次：
```
[root@iZ9458z0ss9Z script]# crontab  -l
*/15 * * * *  cd /data/your_script_path/;./hdd_alarm.sh >/dev/null 2>&1
```

