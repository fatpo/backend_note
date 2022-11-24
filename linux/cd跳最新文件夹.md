## 背景
我有多个地区的目录要进入，比如 id，th，my，tw，sg 等。

然后每个地区的目录，都要进入最新的目录下。

ps：这个目录经常变的。

## 目标
我想要快速进入当前目录的最新目录下。

## 方案
先写一个脚本 jump.sh：
```
#!/bin/bash
cd `ls -ltrh|grep 2022| awk 'END {print $NF}'` 
```

然后在写入环境 zshrc 中， `vi ~/.zshrc`：
```
alias cd5="source /root/diff_total/jump.sh"
```

## 效果
```
cd5
pwd
```
跳来跳去，非常有效。这个真的解决了我多个地区的最新目录跳来跳去的痛点。

## 为什么不直接 alias
最开始的版本是直接在 `vi ~/.zshrc`：
```
alias cd5="cd `ls -ltrh|grep 2022| awk 'END {print $NF}'` "
```
但是无效，每次都只有第一次有效，后面的都认准了第一次的目录，非常讨厌，好像被固化了。

具体原因不懂，所以我才抽成一个 sh 脚本来执行。
