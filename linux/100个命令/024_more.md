## more
和 less 非常相似。 `less is more`.

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

## more 和 less 的区别

* `more`和`less`都支持往后滚动，但是往前滚没有`less`灵活，因为`more`往前滚只能回到第一页。
* 当`more`到达文件末尾时它会自动关闭，而无需按下按钮。而`less`需要手动按q。
* `more`会提示文件进度，如：`--更多--(4%)`， less 不会提示进度。
* 按`=`号的时候，`more` 会提示第几行，`less` 会提示进度+第几行： `jj.txt lines 37-46/47 byte 13083/46422 28%  (press RETURN)`
