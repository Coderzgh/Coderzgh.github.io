---
title: Python网络爬虫实战之二：环境部署、基础语法、文件操作
date: 2019-05-12 09:25:00
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
  

---





# 一、Python的环境部署

Python安装、Python的IDE安装本文不再赘述，网上有很多教程

爬虫必备的几个库：Requests、Selenium、lxml、Beatiful Soup

- Requests 是基于urllib编写的第三方扩展库，是采用Apache2 Licensed开源协议的HTTP库
- Selenium是一个自动化测试工具，利用它我们可以驱动浏览器执行特定的动作，如点击、下拉等操作。对于一些JavaScript渲染的页面来说，这种抓取方式非常有效。
- lxml是Python的一个解析库，支持HTML和XML的解析，支持XPath解析方式，而且解析效率非常高
- Beatiful Soup是Python的一个HTML或XML的解析库，我们可以用它来方便地从网页中提取数据

# 二、Python的基础语法

可参考我的《趣学Python——教孩子学编程》系列笔记

[《趣学Python——教孩子学编程》学习笔记第1-3章](https://www.jianshu.com/p/69b5c9ee926e)

[《趣学Python——教孩子学编程》学习笔记第4-6章](https://www.jianshu.com/p/00850c80f78f)

[《趣学Python——教孩子学编程》学习笔记第7-8章](https://www.jianshu.com/p/90d50cc592ed)

[《趣学Python——教孩子学编程》学习笔记第9-10章](https://www.jianshu.com/p/885cb1fa9827)

[《趣学Python——教孩子学编程》学习笔记第11-12章](https://www.jianshu.com/p/48715dcb524a)

[《趣学Python——教孩子学编程》学习笔记第13章](https://www.jianshu.com/p/c9850ca89b60)

# 三、Python文件的读取与输出

键盘输入

```
# 键盘输入（python3将raw_input和input进行了整合，只有input）
str = input("Please enter:")
print("你输入的内容是：", str)
```

打开文件

```
# 打开一个文件
fo = open(r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao.txt", "wb")
print("文件名：", fo.name)
print("是否已关闭：", fo.closed)
print("访问模式：", fo.mode)
```

关闭文件

```
# 关闭一个文件
fo = open(r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao.txt", "wb")
fo.close()
print("是否已关闭：", fo.closed)
```

写入文件内容

```
# 写入文件内容
fo = open(r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao.txt", "r+")
fo.write("www.baidu.com www.cctvjiatao.com")
fo.flush()
fo.close()
```

读取文件内容

```
fo = open(r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao.txt", "r+")
str = fo.read(11)
print("读取的字符串是：", str)
fo.close()
```

查找当前位置

```
# 查找当前位置
fo = open(r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao.txt", "r+")
str = fo.read(11)
position = fo.tell()
print("当前读取的位置是：", position)
# result: 当前文件位置： 11
fo.close()
```

文件指针重定位

```
# 文件指针重定位
fo = open(r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao.txt", "r+")
str = fo.read(11)
print("读取的字符串1：", str)
# result: 重新读取的字符串1： www.baidu.c
position = fo.tell()
print("当前文件位置：", position)
# result: 当前文件位置： 11
str = fo.read(11)
print("读取的字符串2：", str)
# result: 读取的字符串2： om www.cctv
postion = fo.seek(0, 0)
str = fo.read(11)
print("读取的字符串3：", str)
# result: 读取的字符串3： www.baidu.c
fo.close()
```

文件重命名

```
# 文件重命名 filejiatao.txt——>filejiatao2.txt
import os

src_file = r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao.txt"
dst_file = r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao2.txt"
os.rename(src_file, dst_file)
```

删除文件

```
# 删除一个文件
import os

dirty_file = r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao2.txt"
os.remove(dirty_file)
```





 异常处理1



![img](https:////upload-images.jianshu.io/upload_images/2255795-71be88200641b051.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/627)

2-1.jpg

```
# 异常处理1
try:
    fh = open("testfile.txt", "w")
    fh.write("this is my test file for exception handing!")
except IOError:
    print("Eorror:can\'t find file or read data")
else:
    print("witten content in the file successfully")
    fh.close()
```

异常处理2

```
# 异常处理2
try:
    fh = open("testfile.txt", "w")
    fh.write("this is my test file for exception handing!")
finally:
    print("Eorror:I don\'t kown why ...")
```

异常处理3

```
# 异常处理3
def temp_convert(var):
    try:
        return int(var)
    # except ValueError,Argument:
    #     print("The argument does not contain numbers\n",Argument)
    except (ValueError) as Argument:
        print("The argument does not contain numbers\n", Argument)


temp_convert("xyz")
```

