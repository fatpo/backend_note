## 简单把结构导出来
```
mysqldump -h localhost -uroot -p  -d mydb  > dump.sql
```

怎么看表的 auto_increment:
```
select table_name, AUTO_INCREMENT From INFORMATION_SCHEMA. TABLES where AUTO_INCREMENT is not null;
```


## 更多命令
链接：[https://www.cnblogs.com/qmfsun/p/5405599.html](https://www.cnblogs.com/qmfsun/p/5405599.html)
```
mysqldump -u用戶名 -p密码 -d 数据库名 表名 > 脚本名;

 
导出整个数据库结构和数据
mysqldump -h localhost -uroot -p123456 database > dump.sql



导出单个数据表结构和数据
mysqldump -h localhost -uroot -p123456  database table > dump.sql

 
导出整个数据库结构（不包含数据）
mysqldump -h localhost -uroot -p123456  -d database > dump.sql

 

导出单个数据表结构（不包含数据）
mysqldump -h localhost -uroot -p123456  -d database table > dump.sql



mysqldump 备份导出数据排除某张表
就用 --ignore-table=dbname.tablename参数就行了。

mysqldump -uusername -ppassword -h192.168.0.1 -P3306 dbname --ignore-table=dbname.dbtanles > dump.sql
```

## 导入 mysql
导入到 mysql 中：
```
首先要进入 mysql
mysql> source /tmp/dump2.sql;
```
