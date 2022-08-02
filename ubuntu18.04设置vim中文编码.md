# 背景
某些 ubuntu18.04 竟然 vim 下中文乱码，难以理解。

# 解决
vi ~/.vimrc
```
""""""""""""""""""""""""""""""""""""
"""=>编码设置<="""
""""""""""""""""""""""""""""""""""""
"设置编码"
set encoding=utf-8
set fencs=utf-8,ucs-bom,shift-jis,gb18030,gbk,gb2312,cp936

"设置文件编码"
set fileencodings=utf-8

"设置终端编码"
set termencoding=utf-8

"设置语言编码"
set langmenu=zh_CN.UTF-8
set helplang=cn
```
