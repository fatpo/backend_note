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
