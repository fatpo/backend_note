# 原文链接
[https://juejin.cn/post/6844903924172849166](https://juejin.cn/post/6844903924172849166)

# 安装 jdk
```
sudo apt-get install openjdk-8-jre
```

# 下载
```
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.4.13/zookeeper-3.4.13.tar.gz
```
解压：
```
tar -xvf zookeeper-3.4.13.tar.gz
```

# 找个好位置
```
 mv zookeeper-3.4.13 /usr/local/zookeeper/zookeeper-3.4.13
```
创建软链接（方便更换版本）:
```
ln -s /usr/local/zookeeper/zookeeper-3.4.13 /usr/local/zookeeper/apache-zookeeper
```

# 加到 path
vi ~/.zshrc
```
export ZK_HOME=/usr/local/zookeeper/apache-zookeeper
export PATH=$ZK_HOME/bin:$PATH
```
生效：
```
source ~/.zshrc 
```
