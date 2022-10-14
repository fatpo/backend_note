
## multi_match 案例
```text
GET /_search
{
  "query": {
    "multi_match" : {
      "query":    "kfc", 
      "fields": [ "subject", "message" ] 
    }
  }
}
```
解释：
```text
用"kfc"分别去搜索 subject和 message 字段。
```

## multi_match 类型
| 类型            | 解释                                                                                                                                              |
|:--------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| best_fields   | (default) Finds documents which match any field, but uses the _score from the best field. .<br><br>默认项，匹配到多个 doc，会按照最佳分数去召回 doc。                |
| most_fields   | Finds documents which match any field and combines the _score from each field.  <br><br>假设同时在subject和 message 命中 kfc，会把两个分数加起来。 |
| cross_fields  | Treats fields with the same analyzer as though they were one big field. Looks for each word in any field.                    |
| phrase        | Runs a match_phrase query on each field and uses the _score from the best field.                              |
| phrase_prefix | Runs a match_phrase_prefix query on each field and uses the _score from the best field.   |
| bool_prefix   | Creates a match_bool_prefix query on each field and combines the _score from each field.                        |