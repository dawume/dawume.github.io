title: 「spark 机器学习」读书笔记1
date: 2016-08-04 00:10:13
tags: [spark, 读书笔记]
---
## 前言
最近读完了「spark 机器学习」, 觉得还不错. 虽然内容有些老, 不过翻译还不错. 这里会陆续记录一些笔记.
最近也用spark mllib做了点东西, 感觉坑还是有一些的. 下次专门搞个吐槽帖吧。
下面进入正题, [「spark 机器学习」](https://book.douban.com/subject/26593179/) 这里是豆瓣的链接.

以 IPYTHON 启动 REPL, 可以方便画图和进行一些统计计算
```bash python REPL
> IPYTHON=1 IPYTHON_OPTS="--pylab" ./bin/pyspark
```

一些好用的算子
```python
# countByValue, 相当于 Counter
ages = user_fields.map(lambda x: int(x[1])).countByValue()

# stats 输出一些常用的统计信息
ratings.stats()
Out[27]: (count: 100000, mean: 3.52986, stdev: 1.12566797076, max: 5.0, min: 1.0)

# 生成类别特征到 int 的映射字典
user_dict = user.distinct().zipWithIndex().collectAsMap()
user_dict_bcast = sc.broadcast(user_dict)

# 抽取时间戳
import datetime
hour = datetime.datetime.fromtimestamp(ts).hour
```