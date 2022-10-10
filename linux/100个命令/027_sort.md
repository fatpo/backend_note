## sort
排序。

## 给文件排序
我们先创建一个文件，再分别 `sort 3.txt`和`sort -n 1 3.txt`，前者是字母序，后者是数字序：
```
[root@rddata tmp]#  awk 'BEGIN{for(i=1;i<15;i++){print i}}' | xargs -n 1 echo >> 3.txt
[root@rddata tmp]#
[root@rddata tmp]# sort 3.txt
1
10
11
12
13
14
2
3
4
5
6
7
8
9
[root@rddata tmp]# sort -n 3.txt
1
2
3
4
5
6
7
8
9
10
11
12
13
14
```

## 给管道内容排序
```
[root@rddata tmp]# cat 3.txt  | sort
1
10
11
12
13
14
2
3
4
5
6
7
8
9
```
