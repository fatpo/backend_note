## more
类似 `vi` 和 `cat`，也是查看文件内容的。

不过它支持一些互动操作，可以翻页什么的，比较方便。

和 `less` 非常相似。 `less is more`，这里说一下自己的观点，尽可能用 `less` 而不是 `more`，因为`less`从实操便捷度，隐私性，性能处理上，各方面吊打`more`。

## 演示
交互命令：
* Enter 向下n行，需要定义。默认为1行
* Ctrl+F 向下滚动一屏
* 空格键 向下滚动一屏
* Ctrl+B 返回上一屏
* = 输出当前行的行号
* ：f 输出文件名和当前行的行号
* v 调用vi编辑器
* !命令 调用Shell，并执行命令
* q 退出more

## more 和 less 的关键区别

* `less`可以按键盘上下方向键显示上下内容, `more` 不能通过上下方向键控制显示。
* `less`不必读整个文件，加载速度会比`more`更快。
   ```
   The main difference between more and less is that less command is faster 
   because it does not load the entire file at once 
   and allows navigation though file using page up/down keys.
  ```
* `less`退出后shell不会留下刚显示的内容, 而`more`退出后会在shell上留下刚显示的内容。

## more 和 less 非关键区别
* `more`和`less`都支持往前滚动，但是往后滚没有`less`灵活（比如没有半屏滚动），因为`more`听说很久之前不支持向后滚，不过现在实操下来是支持往后滚的。
* `more` 不支持 `less`的`上下行` 滚动（j 往前，k 往后）。
* 当`more`到达文件末尾时它会自动关闭，而无需按下按钮。而`less`需要手动按q。
* `more`会提示文件进度，如：`--更多--(4%)`， less 不会提示进度。
* 按`=`号的时候，`more` 会提示第几行，`less` 会提示进度+第几行： `jj.txt lines 37-46/47 byte 13083/46422 28%  (press RETURN)`
