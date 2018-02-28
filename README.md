* [服务访问不了或异常慢卡](#服务访问不了或异常慢卡)
* [Java正在运行的进程CPU调优](#java正在运行的进程cpu调优)
* [Python正在运行的进程CPU调优](#python正在运行的进程cpu调优)



# 服务访问不了或异常慢卡
![Alt text](https://github.com/emaste-r/backend_note/blob/master/%E6%9C%8D%E5%8A%A1%E6%8E%92%E6%9F%A5.png)


# Java正在运行的进程CPU调优
先找到CPU异常的PID：
```
top -c
```
然后查看该进程下的线程：
```
top -Hp YOUR_PID
```
然后查看异常的线程：
```
jstack YOUR_THREAD_PID
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

# Python正在运行的进程CPU调优
本人版本：
```
centos7+python2.7.5   
```
安装gdb：
```
sudo yum install gdb
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




