作用：
```
创建目录
mkdir test
mkdir -p test/test ，其中 -p 表示允许创建子目录
```
演示：
```
[root@rddata web]# mkdir test
[root@rddata web]# mkdir -p  test2/test2
[root@rddata web]# tree
.
├── cpu.sh
├── disk.sh
├── load.sh
├── mem.sh
├── server-0.0.1-SNAPSHOT.jar
├── test
├── test2
│   └── test2
└── web.log
```
