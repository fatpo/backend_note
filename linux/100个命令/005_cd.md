作用：
```
进入某一个文件夹的目录
```
常规用法：
```
cd 某一个文件夹
cd 某一个文件夹/它的子文件夹
cd .. 返回上层目录
```
演示：
```
[root@rddata build]# pwd
/home/workspace/web_front/build
[root@rddata build]# cd ..
[root@rddata web_front]# pwd
/home/workspace/web_front
[root@rddata web_front]# cd ..
[root@rddata workspace]# pwd
/home/workspace
[root@rddata workspace]# cd web_front/
[root@rddata web_front]# pwd
/home/workspace/web_front
[root@rddata web_front]#
```
