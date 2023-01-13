## 创建索引
```text
curl -XPUT http://127.0.0.1:9200/fatpo_index/?pretty
```
创建成功：
```json
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "fatpo_index"
}
```

## 创建带 settings 配置的索引
比如初始化索引的时候，指定好副本数和分区数。
```text
curl -XPUT http://127.0.0.1:9200/fatpo_index/?pretty
```
body：
```json
{
    "settings": {
        "number_of_shards": 3,
        "number_of_replicas": 1
    }
}
```
创建成功：
```json
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "fatpo_index"
}
```


## 查看整个索引结构
```text
curl -XGET http://127.0.0.1:9200/fatpo_index/?pretty
```
返回索引结构：
```json
{
  "fatpo_index": {
    "aliases": {},
    "mappings": {},
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
        "number_of_replicas": "1",
        "uuid": "05_-YbotQMiYnI1DqZTxFQ",
        "version": {
          "created": "7160299"
        }
      }
    }
  }
}
```
可以看到 es 的索引基本信息，默认副本 1，默认分区 1。

## 查看索引 mappings
```text
curl -XGET http://127.0.0.1:9200/fatpo_index/_mappings?pretty
```
结果可以看到，我们的索引 mappings 空空如也。
```json
{
    "fatpo_index": {
        "mappings": {}
    }
}
```

## 查看索引 settings
```text
curl -XGET http://127.0.0.1:9200/fatpo_index/_settings?pretty
```
结果可以看到，我们的索引 _settings，默认分区是 1 个，副本是 1 个。
```json
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
        "number_of_replicas": "1",
        "uuid": "05_-YbotQMiYnI1DqZTxFQ",
        "version": {
          "created": "7160299"
        }
      }
    }
  }
}
```
