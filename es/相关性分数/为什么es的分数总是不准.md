# TF&IDF
TF&IDF 相关性算法了解后，发现它有 3 个特点：
* TF: 搜索词组在 field 中的出现次数越高，越相关
* IDF: 搜索词组在全部 doc 的 field 出现的次数越高，越不相关
* Field length : field 越短，越相关

# 不准的点
比如在 title 中查询 java： 
* 在一个 document 中 java 出现了几次
* 在所有的 document 中 java 出现了几次
* 这个 document 的长度

只有第二点的计算，有可能是变化大的，因为所有的 document 的 java 出现次数，默认是只看自己分片的，而不是全部的分片的 documents。

# 解决方案
如果搜索的时候，带了参数 `search_type=dfs_query_then_fetch` 参数， 那么就会计算所有的分片上的 doc 作为 IDF分数。 准确性高了，但是性能差了，差了很多，据官网说。

或者，只设置一个分片 `number_of_shards=1`，但是容错性就没有保证了。
