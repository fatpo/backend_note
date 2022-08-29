## 下载ISO镜像
```
http://ftp.jaist.ac.jp/pub/Linux/CentOS-vault/6.5/isos/x86_64/
```

## 打开windows的Hyper-V
自带的，只要打开服务，就好。


第一步：
```
在 Windows 上，从桌面开始键入程序，单击 "程序和功能" (控制面板 ") Windows 打开或关闭 "hyper-vhyper-v 管理工具"。 然后，单击“确定”。
```

第二步：
```
将服务重新配置为使用 "服务" 桌面应用程序自动启动
打开 "服务" 桌面应用。 (单击 " 开始"，在搜索框中单击，开始键入 " 服务"，然后在结果列表中单击 "服务"。

在详细信息窗格中，右键单击 " Hyper-v 虚拟机管理"，然后单击 " 属性"。

在 " 常规 " 选项卡上，在 " 启动 类型" 中单击 " 自动"。
```

教程：
```
https://docs.microsoft.com/zh-cn/windows-server/virtualization/hyper-v/best-practices-analyzer/The-Hyper-V-Virtual-Machine-Management-service-should-be-configured-to-start
```

## 创建VM
啊，就是傻瓜化，有个快速创建，也有自己创建，可以配置文件夹什么的，点下一步即可。

需要注意的是，我选择第一代就行，第二代就不行。


## 最终效果

![image](https://user-images.githubusercontent.com/6395350/187237156-c9a7d99c-375f-4f87-a93c-ff5bcf272c89.png)
