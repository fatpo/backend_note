## interval
返回按照检索词的特定排列顺序排列的文档。这个查询比较复杂，这里只是简单的介绍，详细的介绍可以看官方文档

比如我们想查询同时包含raj和nayaka的字段并且ray正好在nayaka前面，查询语句如下：
```
POST /_search
{
  "query": {
    "intervals": {
      "name": {
        "match": {
          "query": "raj nayaka",
          "max_gaps": 0,
          "ordered": true
        }
      }
    }
  }
}

```
上面的查询会匹配Raj Nayaka Acchu Valmiki和Yateesh Raj Nayaka。

如果把ordered:true去掉，就会匹配nayaka raj。

如果把max_gaps:0去掉，系统会用默认值-1也就是没有距离要求，就会匹配Raj Raja nayaka或者Raj Kumar Nayaka

其中有两个关键词ordered和max_gaps分别用来控制这个筛选条件是否需要排序以及两个token之间的最大间隔

## curl
```
curl -XGET 'http://127.0.0.1:9200/2022-10-26/_search?pretty'  -H "Content-Type: application/json" --data \
'
{
  "query": {
    "intervals": {
      "content": {
        "match": {
          "query": "need  service",
          "max_gaps": -1,
          "ordered": true
        }
      }
    }
  }
}
'

```

## 链接
[十九种Elasticsearch字符串搜索方式终极介绍](https://zhuanlan.zhihu.com/p/137575167)
