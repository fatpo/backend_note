# 问题
不知道为啥，python3 的默认编码是 latin，导致我的脚本报错：
```
    print(f"\u6ca1\u6709\u4e70\u53f7\uff01\uff01")
UnicodeEncodeError: 'latin-1' codec can't encode characters in position 0-5: ordinal not in range(256)
```

# 解决
```
root@shell: export PYTHONIOENCODING=utf-8
```

# crontab不服气
发现虽然用shell执行python脚本已经没问题了，但是crontab依旧报错，解决方案是加上编码集：
先查看自己的编码：
```
echo $LANG
```
然后crontab -e:
```
55 7 * * * LANG=en_US.UTF-8 /usr/bin/python3 /shell/python1.py >>/tmp/python1.log 2>&1
```
