安装：
```
sudo apt-get install mysql-server
```
改密：
```
$ mysql -u root

mysql> USE mysql; 
mysql> update user set authentication_string=PASSWORD('your_password_here') where user='root';
mysql> update user set plugin="mysql_native_password" where User='root';
mysql> flush privileges;
mysql> quit
```

