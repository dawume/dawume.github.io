title: spark 下分词/序列化问题
date: 2016-08-01 13:45:54
tags: spark
---
scala 下一直找不到称手的分词工具, 这里最终选择了 ansj
pom.xml 加入依赖

```xml pom.xml
<dependency>
    <groupId>org.ansj</groupId>
    <artifactId>ansj_seg</artifactId>
    <version>3.0</version>
</dependency>
 
<dependency>
    <groupId>org.nlpcn</groupId>
    <artifactId>nlp-lang</artifactId>
    <version>1.0.2</version>
</dependency>
```

以下为分词及关键字抽取的代码
```scala test_wordseg.scala
import org.apache.spark.{SparkConf, SparkContext}
import collection.JavaConversions._
import org.ansj.app.keyword.KeyWordComputer
import org.ansj.splitWord.analysis.ToAnalysis
 
class keyword(n: Int) extends KeyWordComputer(n: Int) with Serializable { }
 
object test_wordseg {
  def main(args: Array[String]) {
    val conf = new SparkConf().setMaster("local[*]").setAppName("word_seg")
    val sc = new SparkContext(conf)
    val lines = sc.textFile("/path/to/some_text").cache()
    
    // do word_seg
    val seg = lines.map(ToAnalysis.parse(_).map(_.getName()).mkString(" "))
    seg.take(10).foreach(println)
 
    // do tags exact
    val kw = new keyword(5)
    val tags = lines.map(kw.computeArticleTfidf(_).mkString(" "))  // key word
    tags.take(10).foreach(println)
  }
}
```
这里要注意的是, Scala 要求并行计算的部分都要支持序列化, 如果遇到外部类不支持序列化, 如本例中的 KeyWordComputer 部分, 有两种解决方法.
将 val kw = new KeyWordComputer(5) 部分移入 map 中.
新写一个类继承 KeyWordComputer 同时继承 Serializable, 如本例中的做法.
### 其他
抽空看了下关键字抽取的代码, 略搓.. 之后有空自己搞个吧.