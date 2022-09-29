作用：
```
过滤，筛选出某个关键词。
```
比如我们想过滤下包含 `nginx` 的进程信息：
```
[root@rddata web]# ps -ef | grep nginx
root       53399       1  0 9月17 ?       00:00:00 nginx: master process nginx
root     2243123   53399  0 9月28 ?       00:00:00 nginx: worker process
root     2243124   53399  0 9月28 ?       00:00:00 nginx: worker process
root     2423215 2409741  0 15:46 pts/0    00:00:00 grep --color=auto nginx
```
比如我们想过滤下当前目录包含`.log`后缀的文件名：
```
[root@rddata web]# ll
总用量 24884
-rw-r--r-- 1 root root     1047 9月  28 21:37 cpu.sh
-rw-r--r-- 1 root root       40 9月  28 21:34 disk.sh
-rw-r--r-- 1 root root       37 9月  28 21:35 load.sh
-rw-r--r-- 1 root root       33 9月  28 21:33 mem.sh
-rw-r--r-- 1 root root 25456855 9月  29 00:36 server-0.0.1-SNAPSHOT.jar
-rw-r--r-- 1 root root     3110 9月  29 14:13 web.log


[root@rddata web]# ll | grep *.log
-rw-r--r-- 1 root root     3110 9月  29 14:13 web.log
```
