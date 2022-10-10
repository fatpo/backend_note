## less
类似 `vi`和 `cat`，也是查看文件内容的。

不过它支持一些互动操作，可以翻页什么的，比较方便。

和 `more` 非常相似。 `less is more`，这里说一下自己的观点，尽可能用 `less` 而不是 `more`，因为`less`从实操便捷度，隐私性，性能处理上，各方面吊打`more`。

## 谈谈 less 的由来
```
less was written by a man who was fed up with more's inability to scroll backwards through a file. 
He turned less into an open source project and over time, various individuals added new features to it. 
less is massive now. That's why some small embedded systems have more but not less. 
For comparison, less's source is over 27000 lines long. more implementations are generally only a little over 2000 lines long.
```
有个哥们，因为老版本的 more 不支持向后滚动，重写了一个 less。

然后他开源了，一堆网友往里面加功能，加着加着就2万多行代码，源代码量是 more 的 15 倍多。

一些老款的 linux 系统、嵌入式系统只支持 more，而不支持 less，也有可能是因为 more 小一些，less 太大了。

## 参数

我最喜欢这么用：
```
less -N -m 1.txt 2.txt
```
其中：
```
-N 表示带行数，有这个比较有安全感。
-m 表示和 more 一样，显示进度条
```

## 交互
经验来看，用的最高频就`翻页`和`上下行`两个操作。

翻页操作：
```
ctrl + F - 向前移动一屏
ctrl + B - 向后移动一屏
ctrl + D - 向前移动半屏
ctrl + U - 向后移动半屏
```
上下行操作：
```
j - 下一行
k - 上一行
```
多文件的时候换文件：
```
:n  下一个文件
:p  上一个文件
```
进入`vi`编辑模式：
```
v
```


和 `vi` 一样，按 q 退出。

