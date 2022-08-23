
## OpenSSL 组件：

* libcrypto, libssl主要由开发者使用
* openssl：多用途命令行工具
    * 标准命令
        * 生成用户密码
            * openssl passwd -1 -salt SALT
        * 生成随机数
            * openssl rand -hex NUM
            * openssl rand -base NUM
    * 消息摘要命令（dgst子命令）
    * 加密命令（enc子命令）
        * 对称加密：
            * `openssl enc -e -des3 -a -salt -in fstab -out fstab.ciphertext
                * -e 选择加密算法，可以选：3des, aes, blowfish, towfish
                * -a 采用base64编码
                * -salt 往文件加盐，自动插入一个随机数作为文件内容加密
                * -in 输入文件
                * -out 输出文件
        * 单向加密：
            * openssl dgst -md5 /PATH/TO/SOMEFILE
                * -md5 to use the md5 message digest algorithm 用于签名
        * 公钥加密
            * 加密解密
                * openssl rsautl, gpg
                    * 生成私钥
                        * 无加密：openssl genrsa -out rsa_private_20220823.key 2048
                        * 有加密：openssl genrsa -aes256 -passout pass:111111 -out rsa_aes_private_20220823_withpwd.key 2048
                        * 私钥转非加密：openssl rsa -in rsa_aes_private_20220823_withpwd.key -passin pass:111111 -out
                          rsa_aes_private_20220823_withoutpwd.key
                        * 私钥转加密：openssl rsa -in rsa_aes_private_20220823_withoutpwd.key -aes256 -passout pass:111111
                          -out rsa_aes_private_20220823_withpwd.key
                    * 根据私钥生成公钥
                        * 无加密：openssl rsa -in rsa_private_20220823.key -pubout -out rsa_public_20220823.key
                        * 有加密：openssl rsa -in rsa_aes_private_20220823_withpwd.key -passin pass:111111 -pubout -out
                          rsa_public_20220823_withpwd.key
            * 数字签名
                * openssl rsautl, gpg
        * 密钥交换
            * 生成密钥
                * (umask 077; openssl genrsa -out /PATH/TO/PRIVATE_KEY_FILE NUM_BITS)
            * 提出公钥
                * openssl rsa -in /PATH/FROM/PRIVATE_KEY_FILE -pubout
        * 证书相互转换
          * PEM to DER 
            * openssl x509 -outform der -in certificate.pem -out certificate.der 
          * PEM to PFX 
            * openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt 
          * DER to PEM 
            * openssl x509 -inform der -in certificate.cer -out certificate.pem 
          * PFX to PEM 
            * openssl pkcs12 -in certificate.pfx -out certificate.cer -nodes     
