作用：
```
查看某个文件的后几行
```
ps:
```
对于后台开发同学来说，非常高频的命令，经常用来看日志。
tail -f xxx.log
-f 的意思是监听最新的文件改动，滚动更新。
```
演示：
```
[root@rddata web]# echo -e "1\n2\n3\n4\n5\n" > test.txt
[root@rddata web]#
[root@rddata web]# cat test.txt
1
2
3
4
5

[root@rddata web]#
[root@rddata web]#
[root@rddata web]# head -3 test.txt
1
2
3
[root@rddata web]# tail -3 test.txt
4
5
```
