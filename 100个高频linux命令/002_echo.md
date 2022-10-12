echo 作用：
```
打印某句话。
```

类似`print`:
```
# echo "hello"
hello
```
一般配合管道输送符号（就是 >），把某个打印的东西输送到某个文件中。

举个例子，我们把 "hello world" 写入 "test.txt"：
```
~ > echo "hello world" > test.txt           
```

然后用`cat`命令看看文件`test.txt`内容：
```                                                                                        
~ > cat test.txt                                                                                                                   
hello world
```

![image](https://user-images.githubusercontent.com/6395350/192832561-c6bd6b5d-aee8-4e71-ad37-2e80dc6072a2.png)


