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


## 不想去 CA 机构还想一步到位
如果还想更简单的生成私钥和证书：
```
openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout privkey.pem -out cacert.pem -days 3650
```


参数说明：
* req是证书请求的子命令
* -newkey rsa:2048 -keyout private_key.pem 表示生成私钥(PKCS8格式)；
* -nodes 表示私钥不加密，若不带参数将提示输入密码；
* -x509表示输出证书；
* -days 3650 为有效期，此后根据提示输入证书拥有者信息；
* 若执行自动输入，可使用-subj选项：

参数集合：
```
Usage: req [options]
Valid options are:
 -help               Display this summary
 -inform PEM|DER     Input format - DER or PEM
 -outform PEM|DER    Output format - DER or PEM
 -in infile          Input file
 -out outfile        Output file
 -key val            Private key to use
 -keyform format     Key file format
 -pubkey             Output public key
 -new                New request
 -config infile      Request template file
 -keyout outfile     File to send the key to
 -passin val         Private key password source
 -passout val        Output file pass phrase source
 -rand val           Load the file(s) into the random number generator
 -writerand outfile  Write random data to the specified file
 -newkey val         Specify as type:bits
 -pkeyopt val        Public key options as opt:value
 -sigopt val         Signature parameter in n:v form
 -batch              Do not ask anything during request generation
 -newhdr             Output "NEW" in the header lines
 -modulus            RSA modulus
 -verify             Verify signature on REQ
 -nodes              Don't encrypt the output key
 -noout              Do not output REQ
 -verbose            Verbose output
 -utf8               Input characters are UTF8 (default ASCII)
 -nameopt val        Various certificate name options
 -reqopt val         Various request text options
 -text               Text form of request
 -x509               Output a x509 structure instead of a cert request
                     (Required by some CA's)
 -subj val           Set or modify request subject
 -subject            Output the request's subject
 -multivalue-rdn     Enable support for multivalued RDNs
 -days +int          Number of days cert is valid for
 -set_serial val     Serial number to use
 -addext val         Additional cert extension key=value pair (may be given more than once)
 -extensions val     Cert extension section (override value in config file)
 -reqexts val        Request extension section (override value in config file)
 -precert            Add a poison extension (implies -new)
 -*                  Any supported digest
 -engine val         Use engine, possibly a hardware device
 -keygen_engine val  Specify engine to be used for key generation operations
```
