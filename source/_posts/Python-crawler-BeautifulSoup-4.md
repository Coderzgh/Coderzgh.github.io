---
title: Python网络爬虫实战之四：BeautifulSoup
date: 2019-05-14 09:25:00
author: Demo
img: /source/images/xxx.jpg
top: true
cover: true
coverImg: /images/1.jpg
toc: false
mathjax: false
summary: 
categories: Python
tags:
  - Python
  - BeautifulSoup

---





## 正文：

Beautiful Soup 是一个可以从HTML或XML文件中提取数据的Python库.它能够通过你喜欢的转换器实现惯用的文档导航,查找,修改文档的方式.Beautiful Soup会帮你节省数小时甚至数天的工作时间.

安装: pip install beautifulsoup4

> 名字是beautifulsoup 的包,是 Beautiful Soup3 的发布版本,因为很多项目还在使用BS3, 所以 beautifulsoup 包依然有效.但是如果你在编写新项目,那么你应该安装的 beautifulsoup4，这个包兼容Python2和Python3

## BeautifulSoup的基础使用

下面的一段HTML代码将作为例子被多次用到.这是《爱丽丝梦游仙境》的的一段内容(以后简称文档)

```
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
```

使用BeautifulSoup解析这段代码,能够得到一个 BeautifulSoup 的对象,并能按照标准的缩进格式的结构输出

```
from bs4 import BeautifulSoup

soup = BeautifulSoup(html_doc, "lxml")
print(soup.prettify())
# result:
# <html>
#  <head>
#   <title>
#    The Dormouse's story
#   </title>
#  </head>
#  <body>
#   <p class="title">
#    <b>
#     The Dormouse's story
#    </b>
#   </p>
#   <p class="story">
#    Once upon a time there were three little sisters; and their names were
#    <a class="sister" href="http://example.com/elsie" id="link1">
#     Elsie
#    </a>
#    ,
#    <a class="sister" href="http://example.com/lacie" id="link2">
#     Lacie
#    </a>
#    and
#    <a class="sister" href="http://example.com/tillie" id="link3">
#     Tillie
#    </a>
#    ;
# and they lived at the bottom of a well.
#   </p>
#   <p class="story">
#    ...
#   </p>
#  </body>
# </html>
```

几个简单的浏览结构化数据的方法

```
soup.title
# result: <title>The Dormouse's story</title>

soup.title.name
# result: 'title'

soup.title.string
# result: 'The Dormouse's story'

soup.title.text
# result: 'The Dormouse's story'

soup.title.parent.name
# result: 'head'

soup.p
# result: <p class="title"><b>The Dormouse's story</b></p>

soup.p['class']
# result: 'title'

soup.a
# result: <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

soup.find_all('a')
#  result:
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.find(id="link3")
# result: <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
```

从文档中找到所有<a>标签的链接

```
for link in soup.find_all('a'):
    print(link.get('href'))
# result:
# http://example.com/elsie
# http://example.com/lacie
# http://example.com/tillie
```

从文档中获取所有文字内容

```
print(soup.get_text())
# result:
# The Dormouse's story
#
# The Dormouse's story
#
# Once upon a time there were three little sisters; and their names were
# Elsie,
# Lacie and
# Tillie;
# and they lived at the bottom of a well.
#
# ...
```

从文档中解析导航树

```
# 直接子节点
print(soup.body.contents)

for child in soup.descendants:
    print(child)

# 所有后代节点
for child in soup.descendants:
    print(child)

# 节点内容1-包含换行符
for string in soup.strings:
    print(repr(string))

# 节点内容2-不包含换行符
for string in soup.stripped_strings:
    print(repr(string))

# 兄弟节点(没有一个兄弟节点会返回None)
print(soup.p.next_sibling)
print(soup.p.previous_sibling)

for sibling in soup.a.next_siblings:
    print(repr(sibling))

# 前后节点
print(soup.head.next_element)
print(soup.head.previous_element)

# 父节点
from urllib.request import urlopen

html = urlopen("http://www.pythonscraping.com/pages/page3.html")
bsObj = BeautifulSoup(html)
print(bsObj.find("img", {"src": "../img/gifts/img1.jpg"}).parent.previous_sibling.get_text())
```

从文档中解析CSS选择器

```
print(soup.select('title'))
print(soup.select('a'))
print(soup.select('b'))
print(soup.select('.sisiter'))
print(soup.select('#link1'))
print(soup.select('p #link1'))
print(soup.select('head > title'))
print(soup.select('a[class="sister"]'))
print(soup.select('a[href="http://example.com/elsie"]'))
print(soup.select('p a[href="http://example.com/elsie"]'))
```

结合正则表达式或其他逻辑条件

```
import re

for tag in soup.find_all(href=re.compile("elsie")):
    print(tag)
# result: <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

for tag in soup.find_all("a", class_="sister"):
    print(tag)
# result：
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
# <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

for tag in soup.find_all(["a", "b"]):
    print(tag)
# result :
# <b>The Dormouse's story</b>
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
# <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>


for tag in soup.find_all(True):
    print(tag)


# result:
# <html><head><title>The Dormouse's story</title></head>
# <body>
# <p class="title"><b>The Dormouse's story</b></p>
# <p class="story">Once upon a time there were three little sisters; and their names were
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
# <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
# and they lived at the bottom of a well.</p>
# <p class="story">...</p>
# </body></html>
# <head><title>The Dormouse's story</title></head>
# <title>The Dormouse's story</title>
# <body>
# <p class="title"><b>The Dormouse's story</b></p>
# <p class="story">Once upon a time there were three little sisters; and their names were
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
# <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
# and they lived at the bottom of a well.</p>
# <p class="story">...</p>
# </body>
# <p class="title"><b>The Dormouse's story</b></p>
# <b>The Dormouse's story</b>
# <p class="story">Once upon a time there were three little sisters; and their names were
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
# <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
# and they lived at the bottom of a well.</p>
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
# <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
# <p class="story">...</p>

def has_class_but_no_id(tag):
    return tag.has_attr('class') and not tag.has_attr('id')


for tag in soup.find_all(has_class_but_no_id):
    print(tag)
# result:
# <p class="title"><b>The Dormouse's story</b></p>
# <p class="story">Once upon a time there were three little sisters; and their names were
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
# <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
# and they lived at the bottom of a well.</p>
# <p class="story">...</p>
```

## BeautifulSoup的主要参数的使用

```
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
import bs4
from bs4 import BeautifulSoup

soup = BeautifulSoup(html_doc, "lxml")

# text参数
soup.find_all(text="Elsie")
soup.find_all(text=["Elsie", "Lacie", "Tillie"])
soup.find_all(text=re.compile("Dormouse"))

# limit参数
soup.find_all("a", limit=2)

# recursive参数
soup.html.find_all("title")
soup.html.find_all("title", recursive=False)

# tag对象
print(soup.title)
print(soup.head)
print(soup.a)
print(soup.p)

print(type(soup.a))

print(soup.name)
print(soup.head.name)
print(soup.p.attrs)

print(soup.p['class'])
print(soup.p.get('class'))
soup.p['class'] = "newclass"
print(soup.p)

del soup.p['class']
print(soup.p)

# NavigableString对象
print(soup.p.string)
print(type(soup.p.string))

# BeautifulSoup对象
print(type(soup.name))
print(soup.name)
print(soup.attrs)

# Comment对象
print(soup.a)
print(soup.a.string)
print(type(soup.a.string))

if type(soup.a.string) == bs4.element.Comment:
    print(soup.a.string)
```

## BeautifulSoup解析在线网页实例

```
from urllib.request import urlopen
from bs4 import BeautifulSoup

html = urlopen('http://www.pythonscraping.com/exercises/exercise1.html')
bsObj = BeautifulSoup(html.read(), 'lxml')
print(bsObj.h1)

# CSS属性
from urllib.request import urlopen
from bs4 import BeautifulSoup

html = urlopen('http://www.pythonscraping.com/pages/warandpeace.html')
bsObj = BeautifulSoup(html, 'lxml')
nameList = bsObj.findAll('span', {'class': 'green'})
for name in nameList:
    print(name.get_text())

# find()和findall()
from urllib.request import urlopen
from bs4 import BeautifulSoup

html = urlopen('http://www.pythonscraping.com/pages/warandpeace.html')
bsObj = BeautifulSoup(html)
allText = bsObj.findAll(id='text')
print(allText[0].get_text())
```

更多使用方法参见[BeautifulSoup 中文文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)

