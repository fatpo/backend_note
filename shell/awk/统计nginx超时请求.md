已知我们的nginx的日志配置最后4列：
```
$upstream_connect_time $upstream_header_time  $upstream_response_time $request_time';
```

找出最终结果大于一秒的请求：
```
cat access.log| awk '{if($NF>1){print $0}}'
```

但是能看到一些奇怪的响应时间：
```
0.000 0.076  0.076 1.291
0.000 0.028  0.028 1.601
```
说明什么：
* $upstream_connect_time 是建立连接的时间
* $upstream_header_time 从建立连接到发送第一个响应头字节的时间
* $upstream_response_time 是从建立连接到发送完最后一个内容字节的时间,这个是我们需要关注的,因为客户端的请求和客户所在网络有关
* $request_times 是从请求到建立连接到发送完最后一个内容字节的时间

解读：
```
0.028 1.601 差距这么大，很可能是因为我们网络不好导致的，其实服务器后端已经准备好数据了。
```
