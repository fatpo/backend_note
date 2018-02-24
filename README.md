# 后台开发过程中对运维过程的记录

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

#### 步骤2.1, 打top看服务器整体情况
load average: 0.00, 0.06, 0.11，分别表示在最近1分钟、5分钟、15分钟的负载，1.0以下都算是正常，但是一些老手说最好别超过0.7    
%CPU: 看哪个服务消耗CPU最多      
%MEM: 看哪个服务消耗内存最多      
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
14594 root      20   0 4849092 863276  16848 S   1.0  5.3   4:43.47 java                                                                                    
   13 root      20   0       0      0      0 S   0.7  0.0 963:22.30 rcu_sched                                                                               
 3147 root      20   0  157840   2480   1552 S   0.7  0.0   0:07.57 top                                                                                     
13599 polkitd   20   0  304624  71532   1836 S   0.7  0.4 463:40.66 [celery beat] r                                                                         
19405 polkitd   20   0  298960  82292   4220 S   0.7  0.5   0:19.12 [celeryd: celer                                                                         
20911 root      20   0 4115280 370212  15520 S   0.7  2.3  57:26.54 java                                                                                    
32344 git       20   0 2607972 666360   9172 S   0.7  4.1 154:09.90 ruby      
```

#### 步骤2.2, 打free -m看服务器内存使用情况
主要是看可用内存还剩下几多...还有7个G问题不大...   
```
[root@iZ94won0vbvZ ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:            15G        7.1G        270M        930M        8.2G        7.2G
Swap:           15G        444M         15G
```

#### 步骤2.3，看打df -h硬盘使用情况
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
/dev/dm-3        10G  142M  9.9G   2% /var/lib/docker/devicemapper/mnt/9b60b1947beae2bd1b1138018c623be34f11adfdde148062c9645d94a223dcce
shm              64M     0   64M   0% /var/lib/docker/containers/4d16f00d5481592a69aefda1d1ef300221e51ada5b1045298b99d2cbecced844/shm
/dev/dm-5        10G  142M  9.9G   2% /var/lib/docker/devicemapper/mnt/352b6c78a26060ac1b1c43da06547eff9e27a297c9c249de686f620afe3a822e
shm              64M     0   64M   0% /var/lib/docker/containers/9087b4f83e83ac1a1ac9241c72119734face4adf5d5c73580473465c83fadd39/shm
/dev/dm-8        10G  644M  9.4G   7% /var/lib/docker/devicemapper/mnt/f2bb544090f984828c0557ae19eb839a409d3471cd870e363ad254db8e60639b
shm              64M     0   64M   0% /var/lib/docker/containers/401bd91a54789a279ccb22ff170ed6625601c7bcc0fbb21c86f2a479137f0122/shm
/dev/dm-9        10G  644M  9.4G   7% /var/lib/docker/devicemapper/mnt/202cacbeebbe448ba016c43d70e1a2325ab3d055cf9e28fff67243e36570938c
shm              64M     0   64M   0% /var/lib/docker/containers/364913ef340df3599f4101629740693709f427dee3b6d710098f4e28379ce355/shm
/dev/dm-6        10G  311M  9.7G   4% /var/lib/docker/devicemapper/mnt/7d6fba11a9cd5bd8aa7046858030a32c3862366678e2a0574c7e51b4cdf67106
```
