## 根据用户 id 删除 doc 
```
curl -XPOST http://127.0.0.1:9200/2022-10-16/_delete_by_query  -H "Content-Type: application/json" -d'
{
   "query":{
        "term":{
           "user_id": 123
        }
    }
}
'
```

## 同时删除多索引
```
curl -XPOST http://127.0.0.1:9200/2022-10-15,2022-10-16,2022-10-17/_delete_by_query  -H "Content-Type: application/json" -d'
{
   "query":{
        "term":{
           "user_id": 123
        }
    }
}
'
```

## 校验
```
curl -XPOST http://127.0.0.1:9200/2022-10-16,2022-10-17/_search?pretty  -H "Content-Type: application/json" -d'
{
   "query":{
        "term":{
           "user_id": 123
        }
    }
}
'
```
