---
title: Python网络爬虫实战之一：网络爬虫理论基础
date: 2019-05-11 09:25:00
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





# 一、浏览网页的基本过程和通信基础

> 当我们在浏览器地址栏输入： <http://www.baidu.com> 回车后会浏览器显示百度的首页，那这 段网络通信过程中到底发生了什么？

简单来说这段过程发生了以下四个步骤：

1. 浏览器通过DNS服务器查找域名对应的IP地址;
2. 向IP地址对应的Web服务器发送请求 ;
3. Web Web服务器响应请求，发回HTML页面 ;
4. 浏览器解析HTML内容，并显示出来

### DNS

- DNS 是计算机域名系统 (Domain Name System (Domain Name System 或 Domain Name Service) 的缩写，由解析器和域名服务组成的。
- 域名服务器是指保存有该网络中所主机的和对应 IP 地址，并具有将域名转换为 IP 地址功能的服务器。
- 一般个域名的DNS解析时间在10~60毫秒之间。
- 一个域名必须对应IP地址，而一个IP地址不一定会有域名

### HTTP和HTTPS

- HTTP协议（HyperText Transfer Protocol，超文本传输协议）是一种发布和接收HTML页面的方法
- HTTPS协议（HyperText Transfer Protocol over Secure Socket Layer）简单的讲就是HTTP的安全版，在HTTP下加入SSL层
- SSL（Secure Socket Layer 安全套接层）主要用于Web的安全传输协议，在传输层对网络连接进行加密，保障在Internet上数据传输的安全
- HTTP的端口号是80，HTTPS的端口号是443

### URI与URL

- URI（Uniform Resource Identifier）统一资源标志符
- URL（Universal Resource Locator）统一资源定位符，用于完整地描述Internet上网页和其他资源的地址的一中标识方法
- URL的基本格式：<scheme://host[:port]/path/.../[?query-string][#anchor]> 

### 请求

请求由客户端向服务端发出，分为四部分：请求方法、请求的网址、请求头、请求体

- 请求方法

  

  ![img](https:////upload-images.jianshu.io/upload_images/2255795-fe7eace52135769d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/751)

  1-1.jpg

- 请求头，用来说明服务器使用的附加信息，比较重要的信息有Cookie、Referer、user-Agent等。

  

  ![img](https:////upload-images.jianshu.io/upload_images/2255795-76c4af1982a66838.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/773)

  1-2.jpg

- 请求体，一般承载的内容是POST请求中的表单数据，而对于GET请求，请求体则为空。

  

  ![img](https:////upload-images.jianshu.io/upload_images/2255795-569c648c2c6d1f89.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/819)

  1-3.jpg

### 响应

响应由服务端返回给客户端，分为三部分：响应状态码、响应头、响应提

- 响应状态码

  

  ![img](https:////upload-images.jianshu.io/upload_images/2255795-5804a72cba62b6fd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)

  1-4.jpg

- 响应头，包含了服务器对请求的应答信息，如Content-Type、Server、Set-Cookie等。

  

  ![img](https:////upload-images.jianshu.io/upload_images/2255795-a61e4fb72096b27e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)

  1-4.jpg

- 响应体，响应的正文数据都在响应体中，比如请求网页时他的响应体就是网页的HTML代码，请求一张图片时，它的响应体就是图片的二进制数据。我们做爬虫请求网页后，要解析的就是响应体

# 二、爬虫基本工作原理

### 爬虫基本类型

- 通用爬虫：是 捜索引擎抓取系统（Baidu、Google、Yahoo等）的重要组成部分。主要目的是将互联网上的网页下载到本地，形成一个互联网内容的镜像备份。
- 聚焦爬虫：是"面向特定主题需求"的一种网络爬虫程序，它与通用搜索引擎爬虫的区别在于， 聚焦爬虫在实施网页抓取时会对内容进行处理筛选，尽量保证只抓取与需求相关的网页信息。
- 增量式爬虫：增量式更新指的是在更新的时候只更新改变的地方，而未改变的地方则不更新。所以增量式爬虫技术在爬取网页的过程中，只爬取内容发生变化或是新产生的网页，对未发生内容变化的网页则不会爬取。

### 爬虫的基本工作流程（以通用爬虫为例）



![img](https:////upload-images.jianshu.io/upload_images/2255795-cffe88e6b8b23391.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/265)

​      1-7.png

##### 第一步：抓取网页

- 首先选取一部分的种子URL，将这些URL放入待抓取URL队列；
- 取出待抓取URL，解析DNS得到主机的IP，并将URL对应的网页下载下来，存储进已下载网页库中，并且将这些URL放进已抓取URL队列。
- 分析已抓取URL队列中的URL，分析其中的其他URL，并且将URL放入待抓取URL队列，从而进入下一个循环

##### 第二步：数据存储

- 搜索引擎通过爬虫爬取到的网页，将数据存入原始页面数据库。其中的页面数据与用户浏览器得到的HTML是完全一样的。
- 搜索引擎蜘蛛在抓取页面时，也做一定的重复内容检测，一旦遇到访问权重很低的网站上有大量抄袭、采集或者复制的内容，很可能就不再爬行。

##### 第三步：预处理

- 搜索引擎将爬虫抓取回来的页面，进行各种步骤的预处理，比如：提取文字、中文分词、消除噪音（比如版权声明文字、导航条、广告等……）、索引处理、链接关系计算、特殊文件处理....
- 除了HTML文件外，搜索引擎通常还能抓取和索引以文字为基础的多种文件类型，如 PDF、Word、WPS、XLS、PPT、TXT 文件等。我们在搜索结果中也经常会看到这些文件类型。
- 但搜索引擎还不能处理图片、视频、Flash 这类非文字内容，也不能执行脚本和程序。

##### 第四步：操作数据，实现需求

比如获取京东某类商品的所有评论、购买用户的会员等级

### 爬虫基本结构



![img](https:////upload-images.jianshu.io/upload_images/2255795-f1dc7e8061ac95ed.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/813)

1-6.jpg

### 爬虫的抓取策略

- 深度优先遍历策略
- 广度优先遍历策略
- 方向链接策略
- Partial PageRank 策略
- OPIC策略

### 爬虫的更新策略

- 历史参考策略
- 用户体验策略
- 聚类抽样策略

### 网页分析算法

- 基于用户行为的网页分析算法
- 基于网络拓扑的网页分析算法
- 基于网页内容的网页分析算法

