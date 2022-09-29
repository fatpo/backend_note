作用：
```
显示当前目录下的文件以及相关信息
```
常规用法：
```
ls  输出文件
ls -l  竖屏输出文件
ls -l -a  竖屏输出包括隐藏文件的文件（也可以：ls -la）
ls -l -a -h 竖屏输出包括隐藏文件的文件，并且 size 帮忙换算，友好阅读（也可以：ls -lah）
```

演示：
```
[root@rddata web]# ls
cpu.sh  disk.sh  load.sh  mem.sh  server-0.0.1-SNAPSHOT.jar  test  web.log

[root@rddata web]# ls -l
总用量 24888
-rw-r--r-- 1 root root     1047 9月  28 21:37 cpu.sh
-rw-r--r-- 1 root root       40 9月  28 21:34 disk.sh
-rw-r--r-- 1 root root       37 9月  28 21:35 load.sh
-rw-r--r-- 1 root root       33 9月  28 21:33 mem.sh
-rw-r--r-- 1 root root 25456855 9月  29 00:36 server-0.0.1-SNAPSHOT.jar
drwxr-xr-x 2 root root     4096 9月  29 15:29 test
-rw-r--r-- 1 root root     3110 9月  29 14:13 web.log
[root@rddata web]# ls -l -a
总用量 24896
drwxr-xr-x 3 root root     4096 9月  29 15:29 .
drwxr-xr-x 5 root root     4096 9月  28 16:59 ..
-rw-r--r-- 1 root root     1047 9月  28 21:37 cpu.sh
-rw-r--r-- 1 root root       40 9月  28 21:34 disk.sh
-rw-r--r-- 1 root root       37 9月  28 21:35 load.sh
-rw-r--r-- 1 root root       33 9月  28 21:33 mem.sh
-rw-r--r-- 1 root root 25456855 9月  29 00:36 server-0.0.1-SNAPSHOT.jar
drwxr-xr-x 2 root root     4096 9月  29 15:29 test
-rw-r--r-- 1 root root     3110 9月  29 14:13 web.log
[root@rddata web]# ls -l -a -h
总用量 25M
drwxr-xr-x 3 root root 4.0K 9月  29 15:29 .
drwxr-xr-x 5 root root 4.0K 9月  28 16:59 ..
-rw-r--r-- 1 root root 1.1K 9月  28 21:37 cpu.sh
-rw-r--r-- 1 root root   40 9月  28 21:34 disk.sh
-rw-r--r-- 1 root root   37 9月  28 21:35 load.sh
-rw-r--r-- 1 root root   33 9月  28 21:33 mem.sh
-rw-r--r-- 1 root root  25M 9月  29 00:36 server-0.0.1-SNAPSHOT.jar
drwxr-xr-x 2 root root 4.0K 9月  29 15:29 test
-rw-r--r-- 1 root root 3.1K 9月  29 14:13 web.log
```
