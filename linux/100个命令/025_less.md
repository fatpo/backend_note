## less
类似 `vi`和 `cat`，也是查看文件内容的。

不过它支持一些互动操作，可以翻页什么的，比较方便。

和 `more` 非常相似。 `less is more`，这里说一下自己的观点，尽可能用 `less` 而不是 `more`，因为`less`从实操便捷度，隐私性，性能处理上，各方面吊打`more`。

## 演示

查看 less 的用法：
```
man less
```

上面用法很多，不过我们经验来看，用的最高频就`翻页`和`上下行`两个操作。

翻页操作：
```
ctrl + F - 向前移动一屏
ctrl + B - 向后移动一屏
ctrl + D - 向前移动半屏
ctrl + U - 向后移动半屏
```
话说 f 是 front，b 是 back，这个 d 和 u 是什么缩写呢。

上下行操作：
```
j - 下一行
k - 上一行
```

和 `vi` 一样，按 q 退出。

## more 和 less 的关键区别

* `less`可以按键盘上下方向键显示上下内容, `more` 不能通过上下方向键控制显示。
* `less`不必读整个文件，加载速度会比`more`更快。
   ```
   The main difference between more and less is that less command is faster 
   because it does not load the entire file at once 
   and allows navigation though file using page up/down keys.
  ```
* `less`退出后shell不会留下刚显示的内容, 而`more`退出后会在shell上留下刚显示的内容。
