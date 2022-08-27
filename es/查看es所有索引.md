正常来说：
```
curl 'http://127.0.0.1:9200/_cat/indices'
```

有些设置没开放本地地址访问，只能用IP访问：
```
curl 'http://66.1.2.3:9200/_cat/indices'
```
