## 生成私钥
```
openssl genrsa -des3 -out privkey.pem 2048
```
需要输入密码哦，也可以不要密码：
```
openssl genrsa -out privkey.pem 2048
```

## 根据私钥生成csr
```
openssl req -new -key privkey.pem -out cert.csr
```
这里要填什么国家、省份等基础信息，毕竟 CA 机构要看的。。。
```
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:
```

## CA 机构生成证书
然后拿着这个`cert.csr` 即一个证书请求文件，你可以拿着这个文件去数字证书颁发机构（即CA）申请一个数字证书。CA会给你一个新的文件cacert.pem，那才是你的数字证书。

## 不想去 CA 机构
如果是自己做测试，那么证书的申请机构和颁发机构都是自己。就可以用下面这个命令来生成证书：
```
openssl req -new -x509 -key privkey.pem -out cacert.pem -days 3650  
```
输入密码后，输入国家省份邮箱等信息。

如果还想更简单的生成私钥和证书：
```
openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout privkey.pem -out cacert.pem -days 3650
```
