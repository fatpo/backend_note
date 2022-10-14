## 安装
```text
docker pull elasticsearch:7.16.2
```

## 运行
```text
docker run --name es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms512m -Xmx512m" -d elasticsearch:7.16.2
```

## 验证
```text
curl http://127.0.0.1:9200
{
  "name" : "b870b26d1909",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "eENZg2XtRtqTjTGRYvWxcw",
  "version" : {
    "number" : "7.16.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2b937c44140b6559905130a8650c64dbd0879cfb",
    "build_date" : "2021-12-18T19:42:46.604893745Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```