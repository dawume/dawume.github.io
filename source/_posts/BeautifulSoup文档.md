title: BeautifulSoup文档
date: 2016-08-12 10:52:25
tags: BeautifulSoup
---
记录一下看 BeautifulSoup 文档个人觉得有用的东西

```python
soup.prettify() # 带缩进的结构展示
soup.title # 可以直接用. 访问其中的元素
soup.title.name, soup.title.string # 可以直接获取属性, 内容
soup.title.parent.name # 获取上一级目录

BeautifulSoup(markup, "lxml") # 选择 parser
```

Kinds of objects
```python

```