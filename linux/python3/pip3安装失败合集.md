# 背景
ubuntu18.04

# 问题合集
## 问题 1
```
     src/gevent/libev/corecext.c:95:20: 致命错误：Python.h：没有那个文件或目录
     #include "Python.h"
```
解决方案：
```
apt-get install python3-devel
```

## 问题 2
```
 Attempting uninstall: httplib2
    Found existing installation: httplib2 0.9.2
ERROR: Cannot uninstall 'httplib2'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
```
解决方案：
```
pip3 install firebase_admin  --ignore-installed httplib2
```

## 问题 3
```
protobuf requires Python '>=3.7' but the running Python is 3.6.5
```
解决方案：
```
python3 -m pip install --upgrade pip
```
