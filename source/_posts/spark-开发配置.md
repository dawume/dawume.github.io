title: mac spark 开发配置
date: 2016-07-31 01:57:31
tags: spark
---
总结一下 mac 上的开发配置, 开发环境为 IDEA
首先安装 scala 及 maven
``` bash
# 如果没有 安装 brew 首先需要
# /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew install scala maven
```
### IDEA 配置
首先安装 Scala 插件. 打开菜单, IntelliJ IDEA - Preference - plugins - Install JetBrains plugin, 搜索 scala 安装.

### Maven 配置
``` shell
先拷贝一份配置到 .m2 目录下再进行修改
$ cp /usr/local/Cellar/maven/3.3.3/libexec/conf/settings.xml ~/.m2/settings.xml
```
这里使用了阿里云的服务器, 在 ~/.m2/settings.xml 的 mirrors 及 profiles 两段分别加上以下配置

``` xml
  <mirrors>
    <mirror>
      <id>nexus-osc</id>
      <mirrorOf>*</mirrorOf>
      <name>Nexus osc</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </mirror>
  </mirrors>
  
  <profiles>
    <profile>
      <id>jdk1.8.0_91</id>
      <activation>
        <jdk>1.8.0_91</jdk>
      </activation>
      <repositories>
        <repository>
          <id>nexus</id>
          <name>local private nexus</name>
          <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>nexus</id>
          <name>local private nexus</name>
          <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
```
### 新建项目配置
新建 Maven 项目, Groupid 与 Artifactid 根据需要填写
在左侧项目上右键选择 Add Framework Support - scala
![add scala support](images/spark/add_scala_support.png)
注意如果是用 brew 安装的 scala,  IDEA 下配置 scala 的路径是 /usr/local/Cellar/scala/2.X.X/idea/lib (X 为实际版本)
也可以选择直接用 IDEA 下载, 记得选择 2.10.X 的版本, 否则会与 Spark 冲突

修改 Pom.xml, 根据实际需要填写
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
 
    <groupId>dawu</groupId>
    <artifactId>spark</artifactId>
    <version>1.0-SNAPSHOT</version>
 
    <properties>
        <scala.version>2.10.4</scala.version>
        <spark.version>1.6.0</spark.version>
    </properties>
 
    <dependencies>
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.10</artifactId>
            <version>${spark.version}</version>
        </dependency>
    </dependencies>
 
    <build>
        <plugins>
            <plugin>
                <!--包含这个插件才能编译打包scala程序-->
                <groupId>net.alchim31.maven</groupId>
                <artifactId>scala-maven-plugin</artifactId>
                <version>3.1.0</version>
                <executions>
                    <execution>
                        <id>compile-scala</id>
                        <phase>compile</phase>
                        <goals>
                            <goal>add-source</goal>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>test-compile-scala</id>
                        <phase>test-compile</phase>
                        <goals>
                            <goal>add-source</goal>
                            <goal>testCompile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <!--这个插件允许打包时包含上游依赖-->
                <artifactId>maven-assembly-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <!-- this is used for inheritance merges -->
                        <phase>package</phase>
                        <!-- bind to the packaging phase -->
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```
等待 IDEA 下载对应依赖之后在左侧工程右键选择 maven - Reimport

### 第一个 WordCount 程序

可以在左侧 src/main 目录下添加一个 scala 的文件夹存放 scala 源码, 之后在 菜单 - File - project structure - modules 中, 右键设置 src/main/scala 为 sources 文件夹
在 scala 文件夹下新建 一个 scala 类, 类型为 object. 之后就可以添加代码了, 一个本地运行的 WordCount 的例子

```scala WordCount.scala
import org.apache.spark.{SparkConf, SparkContext}
 
object WordCount {
  def main(args: Array[String]) = {
    val conf = new SparkConf().setAppName("test_WordCount").setMaster("local[*]")
    val sc = new SparkContext(conf)
    val data = sc.textFile("/path/to/load")
    val wordCount = data.flatMap(_.split(" "))
      .map(x => (x, 1))
      .reduceByKey(_ + _)
      .sortBy(-_._2)
    wordCount.take(10).foreach(println)
  }
}
```

其中 /path/to/load 为需要读取的数据
之后 菜单 - run - run 就可以本地运行了.
到这里, 本地 Spark 的开发配置就已经配置好了.
如果需要在线上运行可以将最后一行修改为 wordCount.saveAsTextFile("/path/to/save")

### 相关开发材料
Spark 官方入门指南 [官方入门指南](http://spark.apache.org/docs/latest/programming-guide.html)
繁体中文版 [繁体中文版]( https://taiwansparkusergroup.gitbooks.io/spark-programming-guide-zh-tw/content/index.html)

