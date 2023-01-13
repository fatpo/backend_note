## 修改副本数为 0
```text
curl -XPUT http://127.0.0.1:9200/fatpo_index/_settings?pretty
```
body：
```json
{
  "settings": {
    "index": {
      "number_of_replicas": "2"
    }
  }
}
```
修改成功：
```json
{
    "acknowledged": true
}
```
查看下是否修改成功，可以看到 `  "number_of_replicas": "0",` : 
```text
curl -XGET http://127.0.0.1:9200/fatpo_index/_settings?pretty
{
    "fatpo_index": {
        "settings": {
            "index": {
                "routing": {
                    "allocation": {
                        "include": {
                            "_tier_preference": "data_content"
                        }
                    }
                },
                "number_of_shards": "1",
                "provided_name": "fatpo_index",
                "creation_date": "1665643619571",
                "number_of_replicas": "0",
                "uuid": "05_-YbotQMiYnI1DqZTxFQ",
                "version": {
                    "created": "7160299"
                }
            }
        }
    }
}
```


## 已经创建的索引不能修改分区数量
<font color='red'>注意：已经创建的索引只能修改副本数，不能修改分区数！！！</font>

唯一能够配置分区数量的时候，就是在创建的时候：
```text
PUT /newindex
{
   "settings" : {
      "number_of_shards" : 3, //分区数
      "number_of_replicas" : 1 //副本数
   }
}
```