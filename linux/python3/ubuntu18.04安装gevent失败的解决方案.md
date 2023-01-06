# 问题
```
  x86_64-linux-gnu-gcc -pthread -DNDEBUG -g -fwrapv -O2 -Wall -g -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -fPIC -DLIBEV_EMBED=1 -DEV_COMMON= -DEV_CLEANUP_ENABLE=0 -DEV_EMBED_ENABLE=0 -DEV_PERIODIC_ENABLE=0 -DEV_USE_REALTIME=1 -DEV_USE_MONOTONIC=1 -DEV_USE_FLOOR=1 -I/tmp/pip-build-yu4klgzm/gevent/deps -I/tmp/pip-build-yu4klgzm/gevent/src/gevent/libev -I/tmp/pip-build-yu4klgzm/gevent/deps/libev -Isrc/gevent -Isrc/gevent/libev -Isrc/gevent/resolver -I. -I/usr/include/python3.6m -c src/gevent/libev/corecext.c -o build/temp.linux-x86_64-3.6/src/gevent/libev/corecext.o
  src/gevent/libev/corecext.c:94:10: fatal error: Python.h: No such file or directory
   #include "Python.h"
            ^~~~~~~~~~
  compilation terminated.
  error: command 'x86_64-linux-gnu-gcc' failed with exit status 1

  ----------------------------------------
  Failed building wheel for gevent
  Running setup.py clean for gevent
Failed to build gevent
```

# 解决
升级pip:
```
root@shell:~/www/VirtualSIM_BackEnd# pip3 install pip --upgrade
Collecting pip
  Downloading https://files.pythonhosted.org/packages/a4/6d/6463d49a933f547439d6b5b98b46af8742cc03ae83543e4d7688c2420f8b/pip-21.3.1-py3-none-any.whl (1.7MB)
    100% |################################| 1.7MB 299kB/s
Installing collected packages: pip
  Found existing installation: pip 9.0.1
    Not uninstalling pip at /usr/lib/python3/dist-packages, outside environment /usr
Successfully installed pip-21.3.1
```
再次安装gevent:
```
root@shell:~/www/VirtualSIM_BackEnd# pip3 install 'gevent>=1.5a2'
WARNING: pip is being invoked by an old script wrapper. This will fail in a future version of pip.
Please see https://github.com/pypa/pip/issues/5599 for advice on fixing the underlying issue.
To avoid this problem you can invoke Python with '-m pip' instead of running pip directly.
Collecting gevent>=1.5a2
  Downloading gevent-21.12.0-cp36-cp36m-manylinux_2_12_x86_64.manylinux2010_x86_64.whl (5.6 MB)
     |################################| 5.6 MB 14.3 MB/s
Requirement already satisfied: greenlet<2.0,>=1.1.0 in /usr/local/lib/python3.6/dist-packages (from gevent>=1.5a2) (1.1.2)
Requirement already satisfied: zope.event in /usr/local/lib/python3.6/dist-packages (from gevent>=1.5a2) (4.5.0)
Requirement already satisfied: zope.interface in /usr/lib/python3/dist-packages (from gevent>=1.5a2) (4.3.2)
Requirement already satisfied: setuptools in /usr/local/lib/python3.6/dist-packages (from gevent>=1.5a2) (59.6.0)
Installing collected packages: gevent
Successfully installed gevent-21.12.0
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
```
