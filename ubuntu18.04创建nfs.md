# 背景
有个需求需要多台服务存储本地文件，因为服务器要无状态嘛，又要共享，只能上 nfs 了。

nfs 是什么？
```
network file system，你把它当成一个网络版的本地文件系统即可。
```

# 玩转nfs

## 服务端
安装服务端：
```
sudo apt install nfs-kernel-server
```
配置`/etc/exports`，NFS挂载目录及权限由/etc/exports文件定义：
```
root@shell:# cat /etc/exports
# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/data/uploads *(rw,sync,no_root_squash,no_subtree_check)
```
重启服务：
```
sudo /etc/init.d/nfs-kernel-server restart
```
检查下`111`, `2049`两个端口是否开启：
```
root@shell:# netstat -ntlp | grep -E '111|2049'
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      27395/rpcbind
tcp        0      0 0.0.0.0:2049            0.0.0.0:*               LISTEN      -
tcp6       0      0 :::111                  :::*                    LISTEN      27395/rpcbind
tcp6       0      0 :::2049                 :::*                    LISTEN      -
```


## 客户端
安装客户端：
```
sudo apt install nfs-common
```
配置 nfs 挂载：
```
mount -t nfs 1.2.3.4:/data/uploads ./uploads
```

自此，ubuntu18.04 就可以玩转 nfs 啦。

后面的只是磁盘相关的知识，可以不看。


# 挂载磁盘


## 查看当前磁盘
`fdisk -l`：查看所有被系统识别的磁盘
```
root@shell:# fdisk -l
Disk /dev/vda: 25 GiB, 26843545600 bytes, 52428800 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x90cfdf96

Device     Boot Start      End  Sectors Size Id Type
/dev/vda1  *     2048 52428766 52426719  25G 83 Linux


Disk /dev/vdb: 40 GiB, 42949672960 bytes, 83886080 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```
可以看到 2 个盘，我们刚买的 40GB 的盘是 `/dev/vdb`。

或者用`lsblk`看磁盘，也能看到两个磁盘，其中 vdb 还没挂载好呢：
```
root@shell:# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sr0     11:0    1 1024M  0 rom
vda    252:0    0   25G  0 disk
`-vda1 252:1    0   25G  0 part /
vdb    252:16   0   40G  0 disk
```

## 磁盘科普
磁盘分区类型：
* MSDOS，早期兼容微软的磁盘
* GPT，GUID partition table, For a new disk, I recommend gpt: it allows more partitions, it can be booted even in pre-UEFI systems (using grub), and supports disks larger than 2 TiB (up to 8 ZiB for 512-byte sector disks).

## 新买的磁盘怎么玩

指定分区类型 Choose a Partitioning Standard：
```
parted -s /dev/vdb mklabel gpt
```
创建分区 Create the New Partition:
```
parted -s /dev/vdb unit mib mkpart primary 0% 100%
```

创建后检查下，`lsblk`看到 vdb 盘已经处理完毕：
```
root@shell:~# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sr0     11:0    1 1024M  0 rom
vda    252:0    0   25G  0 disk
`-vda1 252:1    0   25G  0 part /
vdb    252:16   0   40G  0 disk
`-vdb1 252:17   0   40G  0 part
```

挂载磁盘 `/dev/vdb1` 到 `/data` 目录下：
```
# mkdir /data
# echo >> /etc/fstab
# echo /dev/vdb1                /data     ext4    defaults,noatime,nofail 0 0 >> /etc/fstab
# mount  /data
```
其中 `/etc/fstab` 处理自动挂载的，不然下次重启又要手动挂一次。
