用`--header 'Authorization' `：
```
curl --header 'Authorization: Basic bzJvYWxnb19mb29kX3NlYXJjaDpw'  -XPOST 'http://127.0.0.1:9201/fatpo_index/_search?pretty' -H "Content-Type: application/json" --data '
{
    "size" : 100,
    "query":{
        "match": {
            "store_name": "test"
        }
    }
}
'
```
或者`--user username:password`：
```
curl --user username:password  -XPOST 'http://127.0.0.1:9201/fatpo_index/_search?pretty' -H "Content-Type: application/json" --data '
{
    "size" : 100,
    "query":{
        "match": {
            "store_name": "test"
        }
    }
}
'
```
