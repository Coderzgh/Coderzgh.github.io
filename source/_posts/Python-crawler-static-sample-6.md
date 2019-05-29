---
title: Python网络爬虫实战之六：静态网页爬取案例实战
date: 2019-05-18 09:25:00
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

- ## 正文：

  ## 预备知识点：正则表达式之 pattern+?、pattern*?、(?!pattern)、(?:pattern)

  ### pattern+?、pattern*?

  这两个比较常用，表示懒惰匹配，即匹配符合条件的尽量短的字符串。默认情况下 + 和 * 是贪婪匹配，即匹配尽可能长的字符串，在它们后面加上 ? 表示想要进行懒惰匹配。

  ### (?!pattern)

  表示一个过滤条件，若字符串符合 pattern 则将其过滤掉。在分析日志时很有用，例如想过滤掉包含 info 标记的日志可以写 ^(?!.*info).*$。

  ### (?:pattern)

  这条规则主要是为了优化性能，对匹配没有影响。它表示括号内的子表达式匹配的结果不需要返回也不会被 ![1](https://math.jianshu.com/math?formula=1)2 之类的反向引用。

  ## 案例实战一

  获取网页<https://en.wikipedia.org/wiki/Kevin_Bacon>中的词条url链接

  ### 尝试1

  ```
  ## 尝试1：爬取网页内容
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  
  html = urlopen(r'https://en.wikipedia.org/wiki/Kevin_Bacon')
  bsObj = BeautifulSoup(html, 'lxml')
  print(bsObj.prettify())
  ```

  ### 尝试2

  ```
  ## 尝试2:抓取网页中的 a 标签，且以 href 开头的所有url链接
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  
  html = urlopen(r'https://en.wikipedia.org/wiki/Kevin_Bacon')
  bsObj = BeautifulSoup(html, 'lxml')
  allLinks = bsObj.findAll("a")
  print(len(allLinks))  # 780
  for link in allLinks:
      if "href" in link.attrs:
          print(link.attrs["href"])
  ```

  > 通过分析“尝试2”中的数据，发现如下规律：
  >
  > 1、id在bodyContent的div标签中；
  >
  > 2、url不包含冒号；
  >
  > 3、url以/wiki/开头

  ### 正式提取

  ```
  ## 正式提取维基中的词条url
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  import re
  
  html = urlopen(r'https://en.wikipedia.org/wiki/Kevin_Bacon')
  bsObj = BeautifulSoup(html, 'lxml')
  allLinks = bsObj.find('div', {'id': 'bodyContent'}).findAll('a', href=re.compile("^(/wiki/)((?!:).)*$"))
  # allLinks
  # allLinks[0]
  # # result: <a class="mw-disambig" href="/wiki/Kevin_Bacon_(disambiguation)" title="Kevin Bacon (disambiguation)">Kevin Bacon (disambiguation)</a>
  # allLinks[0].text
  # # result: 'Kevin Bacon (disambiguation)'
  # allLinks[0].attrs['class']
  # # result: ['mw-disambig']
  # allLinks[0].attrs['href']
  # # result: '/wiki/Kevin_Bacon_(disambiguation)'
  # allLinks[0].attrs['title']
  # # result: 'Kevin Bacon (disambiguation)'
  # print(len(allLinks))  # 380
  for link in allLinks:
      if 'href' in link.attrs:
          print(link.attrs['href'])
  ```

  ## 案例实战二

  获取网页<https://en.wikipedia.org/wiki/Kevin_Bacon>中的词条url链接以及关联页面中的词条url

  ```
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  import re
  import datetime
  import random
  
  
  def getLinks(articleUrl):
      html = urlopen("https://en.wikipedia.org" + articleUrl)
      bsObj = BeautifulSoup(html, 'lxml')
      return bsObj.find('div', {'id': 'bodyContent'}).findAll('a', href=re.compile("^(/wiki/)((?!:).)*$"))
  
  
  links = getLinks("/wiki/Kevin_Bacon")
  random.seed(datetime.datetime.now())  # 设置随机因子为系统时间，这样再使用随机函数时就不会取到重复值了
  while len(links) > 0:
      newArticle = links[random.randint(0, len(links) - 1)].attrs["href"]
      print(newArticle)
      links = getLinks(newArticle)
  ```

  > 上述代码存在两个隐患：
  >  隐患1，有可能会从A页面到B页面再到A页面；
  >  隐患2，没有做异常处理

  ## 案例实战三

  获取词条以及关联词条的url、标题、概要

  ```
  pages = set()  # url容器，作用：收集新的url链接；剔除已抓取的url
  
  
  def getLinks(pageUrl):
      global pages  # 设为全局变量
      html = urlopen("https://en.wikipedia.org" + pageUrl)
      bsObj = BeautifulSoup(html, "lxml")
      try:
          # 维基词条页面的特征：
          # 1、所有的标题都在h1->span标签里，而且只有一个标题标签
          # 2、正文文字在div#bodyContent标签里；第一段文字 div#mw-content-text->p
          # 3、编辑链接只出现在词条页面上，位于li#ca-edit标签里的 li#caedit->span->a
          print("https://en.wikipedia.org" + pageUrl)
          print(bsObj.h1.get_text())
          print(bsObj.find(id="mw-content-text").findAll("p")[0])
          print(bsObj.find(id="ca-edit").find("span").find("a").attrs['href'])
      except AttributeError:
          print("This page is missing something! No worries though!")
  
      allLinks = bsObj.find_all("a", href=re.compile("^(/wiki/)((?!:).)*$"))
      for link in allLinks:
          if 'href' in link.attrs:
              newPage = link.attrs['href']
              print("------------------------\n" + newPage)
              pages.add(newPage)
              getLinks(newPage)
  
  
  getLinks("/wiki/Farouk_Topan")
  ```

  ## 案例实战四

  通过定义函数，获取页面内的所有外链

  ```
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  import datetime
  import random
  import re
  
  random.seed(datetime.datetime.now())
  allExtLinks = set()
  allIntLinks = set()
  
  
  # 获取页面所有内链的列表
  def getInternalLinks(bsObj, includeUrl):
      internalLinks = []
      # Finds all links that begin with a "/"
      for link in bsObj.findAll("a", href=re.compile("^(/.*" + includeUrl + ")")):
          if link.attrs["href"] is not None:
              if link.attrs["href"] not in internalLinks:
                  internalLinks.append(link.attrs["href"])
      return internalLinks
  
  
  # 获取所有外链的列表
  def getExternallLinks(bsObj, excludeUrl):
      externalLinks = []
      # Finds all links that start with "http" or "www" that do not contain the current URL
      for link in bsObj.findAll("a", href=re.compile("^(http|www)((?!" + excludeUrl + ").)*$")):
          if link.attrs["href"] is not None:
              if link.attrs['href'] not in externalLinks:
                  externalLinks.append(link.attrs['href'])
      return externalLinks
  
  
  # url拆分
  def splitAddress(address):
      addressParts = address.replace("http://", "").split("/")
      return addressParts
  
  
  # Collects a list of all external URLs found on the site
  def getAllExternalLinks(siteUrl):
      html = urlopen(siteUrl)
      bsObj = BeautifulSoup(html, 'lxml')
      internalLinks = getInternalLinks(bsObj, splitAddress(siteUrl)[0])
      externalLinks = getExternallLinks(bsObj, splitAddress(siteUrl)[0])
  
      for link in externalLinks:
          if link not in allExtLinks:
              allExtLinks.add(link)
              print(link)
      for link in internalLinks:
          if link not in allIntLinks:
              allIntLinks.add(link)
              getAllExternalLinks("http:" + link)
  
  
  getAllExternalLinks("http://oreilly.com")
  ```

  