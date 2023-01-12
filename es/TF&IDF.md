es 的相关性得分，默认是 TF&IDF 算法，全程是 Term Frequency 和 Inverse Doc Frequency。

它就 3 个特点：
* 搜索文本中的各个词条在 field 文本中出现了多少次，出现次数越多，就越相关
* 搜索文本中的各个词条在整个索引的所有文档中出现了多少次，出现的次数越多，就越不相关
* field 长度，field 越长，相关度越弱
