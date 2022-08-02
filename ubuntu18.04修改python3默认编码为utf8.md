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
