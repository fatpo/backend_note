# 背景
文件列表如下，需要对它进行 rename 操作：

文件如下：
```
root@shell:# ll
total 12
drwxr-xr-x 2 root root 4096 Aug  3 10:53 ./
drwxr-xr-x 1 root root 4096 Aug  3 10:53 ../
-rw-r--r-- 1 root root    0 Aug  3 10:53 hallo.txt
-rw-r--r-- 1 root root    0 Aug  3 10:53 hallo2.txt
```

# linux
几个点注意下：
* 我们使用find命令来找到文件，因为 find 命令出来的结果是比较纯粹的 filename
  ```
  root@shell:#  find . -name "*hallo*"
  ./hallo2.txt
  ./hallo.txt
  ```
* xargs 的 -i 表示替换，{}表示入参 (你会问为啥不是-r...)
  ```
  -I R                         same as --replace=R
  -i, --replace[=R]            replace R in INITIAL-ARGS with names read
                                 from standard input; if R is unspecified,
                                 assume {}
  ```
* `mv \"{}\" \"{}\"` 出来的结果是 `mv  hallo.txt hallo.txt`, 我们要把第二个hallo.txt换成hello.txt，sed 要匹配第二个用的是2g


最终命令和结果如下：
```
root@shell:# find . -name '*hallo*' | xargs -i echo mv \"{}\" \"{}\" | sed "s/hallo/hello/2g" | sh
root@shell:# ll
total 12
drwxr-xr-x 2 root root 4096 Aug  3 11:01 ./
drwxr-xr-x 1 root root 4096 Aug  3 10:53 ../
-rw-r--r-- 1 root root    0 Aug  3 10:53 hello.txt
-rw-r--r-- 1 root root    0 Aug  3 10:53 hello2.txt
```

# macos
大差不差，但是 mac 下的xargs命令不支持 -i，所以要迂回替换下：
```
root@shell:# find . -name "hallo*" | xargs -n1 -I{} echo mv {} {} | sed 's/hallo/hello/2' | sh 
root@shell:# ll                                                                                
total 0
-rw-r--r--  1 jianguo.ouyang  staff     0B  8  3 11:18 hello.txt
-rw-r--r--  1 jianguo.ouyang  staff     0B  8  3 11:18 hello2.txt
```

区别：
* mac 下的 sed 和 linux 下不大一样，linux 下是 `s/hallo/hello/2g`, mac 下是 `s/hallo/hello/2
* mac 下的 xargs 也不大一样，mac 的 `xargs -I{}` 等价于linux的 `xargs -i`
* xargs -n1 表示让命令一个个传递，逐个处理，不要一股脑都传过来批量处理
