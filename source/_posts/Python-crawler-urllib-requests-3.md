---
title: Python网络爬虫实战之三：基本工具库urllib和requests
date: 2019-05-13 09:25:00
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
  - requests
  - urllib
---





# Python网络爬虫实战之三：基本工具库urllib和requests

# 一、urllib

### urllib简介

urllib是Python中一个功能强大用于操作URL，并在爬虫时经常用到的一个基础库，无需额外安装，默认已经安装到python中。

### urllib在python2.x与python3.x中的区别

在python2.x中，urllib分为urllib和urllib2，在python3.x中合并为urllib。两者使用起来不太一样，注意转换。

| Python2.x           | Python3.x                                         |
| ------------------- | ------------------------------------------------- |
| import urllib2      | import urllib.request，urllib.error               |
| import urllib       | import urllib.request，urllib.error，urllib.parse |
| import urlparse     | import urllib.parse                               |
| import urlopen      | import urllib.request.urlopen                     |
| import urlencode    | import urllib.parse.urlencode                     |
| import urllib.quote | import urllib.request.quote                       |
| cookielib.CookieJar | http.CookieJar                                    |
| urllib2.Request     | urllib.request.Request                            |

### urllib的四个子模块

Python3.6.0中urllib模块包括一下四个子模块，urllib模块是一个运用于URL的包（urllib is a package that collects several modules for working with URLs）

- urllib.request用于访问和读取URLS（urllib.request for opening and reading URLs），就像在浏览器里输入网址然后回车一样，只需要给这个库方法传入URL和其他参数就可以模拟实现这个过程。
- urllib.error包括了所有urllib.request导致的异常（urllib.error containing the exceptions raised by urllib.request），我们可以捕捉这些异常，然后进行重试或者其他操作以确保程序不会意外终止。
- urllib.parse用于解析URLS（urllib.parse for parsing URLs），提供了很多URL处理方法，比如拆分、解析、合并、编码。
- urllib.robotparser用于解析robots.txt文件（urllib.robotparser for parsing robots.txt files），然后判断哪些网站可以爬，哪些网站不可以爬。

### 使用urllib打开网页

最基本的方法打开网页

```
# 最基本的方法打开网页
from urllib.request import urlopen

response = urlopen("http://www.baidu.com")
print(type(response))
print(response.status)
print(response.getheaders())
print(response.getheader('Server'))
html = response.read()
print(html)
```

携带data参数打开网页

```
# 携带data参数打开网页
from urllib.parse import urlencode
from urllib.request import urlopen

data = bytes(urlencode({'word': 'hello'}), encoding='utf8')
response = urlopen('http://httpbin.org/post', data=data)
print(response.read().decode('utf-8'))
```

携带timeout参数打开网页1

```
#  携带timeout参数打开网页1
from urllib.request import urlopen

# response = urllib.request.urlopen('http://httpbin.org/get', timeout=0.1)
response = urlopen('http://httpbin.org/get', timeout=1)
print(response.read())
```

携带timeout参数打开网页2

```
# 携带timeout参数打开网页2
from urllib.request import urlopen

try:
    response = urlopen('http://httpbin.org/get', timeout=0.1)
    print(response.read())
except Exception as e:
    print(e)
```

通过构建Request打开网页1

```
# 通过构建Request打开网页1
from urllib.request import Request
from urllib.request import urlopen

request = Request('https://python.org')
response = urlopen(request)
print(response.read().decode('utf-8'))
```

通过构建Request打开网页2

```
# 通过构建Request打开网页2
from urllib.request import Request
from urllib.request import urlopen
from urllib.parse import urlencode

url = 'http://httpbin.org/post'
headers = {
    'User-Agent': 'Mozilla/4.0(compatibe;MSIE 5.5;Windows NT)',
    'Host': 'httpbin.org'
}
dict = {'name': 'Germey'}
data = bytes(urlencode(dict), encoding='utf8')
req = Request(url=url, data=data, headers=headers, method='POST')
response = urlopen(req)
print(response.read().decode('utf-8'))
```

与通过构建Request打开网页2对比

```
# 与通过构建Request打开网页2对比
from urllib.request import Request
from urllib.request import urlopen

req = Request(url=url, data=data, method='POST')
response = urlopen(req)
print(response.read().decode('utf-8'))
```

通过构建Request打开网页3：通过add_header方

```
# 通过构建Request打开网页3：通过add_header方法添加headers
from urllib.request import Request
from urllib.request import urlopen
from urllib.parse import urlencode

url = 'http://httpbin.org/post'
dict = {'name': 'Germey'}
data = bytes(urlencode(dict), encoding='utf8')
req = Request(url=url, data=data, method='POST')
req.add_header('User-Agent', 'Mozilla/4.0(compatibe;MSIE 5.5;Windows NT)')
response = urlopen(req)
print(response.read().decode('utf-8'))
```

urlencode()的使用

```
# urlencode()的使用
from urllib.parse import urlencode
from urllib.request import urlopen
data = {'first': 'true', 'pn': 1, 'kd': 'Python'}
data = urlencode(data).encode('utf-8')
data
page = urlopen(req, data=data).read()
page
```

使用代理打开网页

```
# 使用代理
from urllib.error import URLError
from urllib.request import ProxyHandler, build_opener

proxy_handler = ProxyHandler({'http': '106.56.102.140:8070'})
opener = build_opener(proxy_handler)
try:
    response = opener.open('http://www.baidu.com/')
    print(response.read().decode('utf-8'))
except URLError as e:
    print(e.reason)
```

# 二、requests

相比较urllib模块，requests模块要简单很多，但是需要单独安装：

- 在windows系统下只需要在命令行输入命令 pip install requests 即可安装。
- 在 linux 系统下，只需要输入命令 sudo pip install requests ，即可安装。

requests库的八个主要方法

| 方法               | 描述                            |
| ------------------ | ------------------------------- |
| requests.request() | 构造一个请求，支持以下各种方法  |
| requests.get()     | 向html网页提交get请求的方法     |
| requests.post()    | 向html网页提交post请求的方法    |
| requests.head()    | 获取html头部信息的主要方法      |
| requests.put()     | 向html网页提交put请求的方法     |
| requests.options() | 向html网页提交options请求的方法 |
| requests.patch()   | 向html网页提交局部修改的请求    |
| requests.delete()  | 向html网页提交删除的请求        |

请求之后，服务器通过response返回数据，response具体参数如下图：

| 属性                | 描述                                             |
| ------------------- | ------------------------------------------------ |
| r.status_code       | http请求的返回状态，若为200则表示请求成功        |
| r.text              | http响应内容的字符串形式，即返回的页面内容       |
| r.encoding          | 从http header 中猜测的相应内容编码方式           |
| r.apparent_encoding | 从内容中分析出的响应内容编码方式（备选编码方式） |
| r.content           | http响应内容的二进制形式                         |

### requests.request(method, url, **kwargs)

- method：即 get、post、head、put、options、patch、delete
- url：即请求的网址
- **kwargs：控制访问的参数，具体参数如下：
- - params：字典或字节序列，作为参数增加到url中。使用这个参数可以把一些键值对以?key1=value1&key2=value2的模式增加到url中

```
## request(method, url, **kwargs)，当 **kwargs 为 params
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.request('GET', 'http://httpbin.org/get', params=payload)
print(r.url)
# result: http://httpbin.org/get?key1=value1&key2=value2
print(r.text)
# result:
# {
#   "args": {
#     "key1": "value1", 
#     "key2": "value2"
#   }, 
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Connection": "close", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.19.1"
#   }, 
#   "origin": "1.203.183.95", 
#   "url": "http://httpbin.org/get?key1=value1&key2=value2"
# }
```

- - data：字典，字节序或文件对象，重点作为向服务器提供或提交资源是提交，作为request的内容，与params不同的是，data提交的数据并不放在url链接里， 而是放在url链接对应位置的地方作为数据来存储。它也可以接受一个字符串对象。

```
## request(method, url, **kwargs)，当 **kwargs 为 data
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.request('POST', 'http://httpbin.org/post', data=payload)
print(r.url)
# result: http://httpbin.org/post
print(r.text)
# result:
# {
#   "args": {}, 
#   "data": "", 
#   "files": {}, 
#   "form": {
#     "key1": "value1", 
#     "key2": "value2"
#   }, 
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Connection": "close", 
#     "Content-Length": "23", 
#     "Content-Type": "application/x-www-form-urlencoded", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.19.1"
#   }, 
#   "json": null, 
#   "origin": "1.203.183.95", 
#   "url": "http://httpbin.org/post"
# }
```

- - json：json格式的数据， json合适在相关的html，http相关的web开发中非常常见， 也是http最经常使用的数据格式， 他是作为内容部分可以向服务器提交。

```
## request(method, url, **kwargs)，当 **kwargs 为 json
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.request('POST', 'http://httpbin.org/post', json=payload)
print(r.url)
# result: http://httpbin.org/post
print(r.text)
# result:
# {
#   "args": {}, 
#   "data": "{\"key1\": \"value1\", \"key2\": \"value2\"}", 
#   "files": {}, 
#   "form": {}, 
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Connection": "close", 
#     "Content-Length": "36", 
#     "Content-Type": "application/json", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.19.1"
#   }, 
#   "json": {
#     "key1": "value1", 
#     "key2": "value2"
#   }, 
#   "origin": "1.203.183.95", 
#   "url": "http://httpbin.org/post"
# }
```

- - files：字典， 是用来向服务器传输文件时使用的字段。

```
## request(method, url, **kwargs)，当 **kwargs 为 file
import requests
# filejiatao.txt 文件的内容是文本“www.baidu.com www.cctvjiatao.com”
files = {'file': open(r"D:\DataguruPyhton\PythonSpider\lesson2\filejiatao.txt", "rb")}
r = requests.request('POST', 'http://httpbin.org/post', files=files)
print(r.url)
# result: http://httpbin.org/post
print(r.text)
# result:
# {
#   "args": {}, 
#   "data": "", 
#   "files": {
#     "file": "www.baidu.com www.cctvjiatao.com"
#   }, 
#   "form": {}, 
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Connection": "close", 
#     "Content-Length": "182", 
#     "Content-Type": "multipart/form-data; boundary=ee12ea6a4fd2b8a3318566775f2b268f", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.19.1"
#   }, 
#   "json": null, 
#   "origin": "1.203.183.95", 
#   "url": "http://httpbin.org/post"
# }
```

- - headers：字典是http的相关语，对应了向某个url访问时所发起的http的头字段， 可以用这个字段来定义http的访问的http头，可以用来模拟任何我们想模拟的浏览器来对url发起访问。

```
## request(method, url, **kwargs)，当 **kwargs 为 headers
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36"}
r = requests.request('GET', 'http://httpbin.org/get', params=payload, headers=headers)
print(r.url)
# result: http://httpbin.org/get?key1=value1&key2=value2
print(r.text)
# result:
# {
#   "args": {
#     "key1": "value1", 
#     "key2": "value2"
#   }, 
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Connection": "close", 
#     "Host": "httpbin.org", 
#     "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36"
#   }, 
#   "origin": "1.203.183.95", 
#   "url": "http://httpbin.org/get?key1=value1&key2=value2"
# }
```

- - cookies：字典或CookieJar，指的是从http中解析cookie

```
## request(method, url, **kwargs)，当 **kwargs 为 cookies
import requests

cookies = dict(cookies_are='working')
r = requests.request('GET', 'http://httpbin.org/cookies', cookies=cookies)
print(r.url)
# result: http://httpbin.org/cookies
print(r.text)
# result:
# {
#   "cookies": {
#     "cookies_are": "working"
#   }
# }
```

- - auth：元组，用来支持http认证功能

```
## request(method, url, **kwargs)，当 **kwargs 为 auth
import requests

cs_user = '用户名'
cs_psw = '密码'
r = requests.request('GET', 'https://api.github.com', auth=(cs_user, cs_psw))
print(r.url)
# result: 待补充
print(r.text)
# result: 待补充
```

- - timeout: 用于设定超时时间， 单位为秒，当发起一个get请求时可以设置一个timeout时间， 如果在timeout时间内请求内容没有返回， 将产生一个timeout的异常。

```
## request(method, url, **kwargs)，当 **kwargs 为 timeout
import requests

r = requests.request('GET', 'http://github.com', timeout=0.001)
print(r.url)
# result: 报错 socket.timeout: timed out
```

- - proxies：字典， 用来设置访问代理服务器。

```
## request(method, url, **kwargs)，当 **kwargs 为 proxies
import requests

proxies = {
    'https': 'http://41.118.132.69:4433'
}
# 也可以通过环境变量设置代理
# export HTTP_PROXY='http://10.10.1.10:3128'
# export HTTPS_PROXY='http://10.10.1.10:1080'
r = requests.request('GET', 'http://httpbin.org/get', proxies=proxies)
print(r.url)
# result: http://httpbin.org/get
print(r.text)
# result:
# {
#   "args": {}, 
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Connection": "close", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.19.1"
#   }, 
#   "origin": "1.203.183.95", 
#   "url": "http://httpbin.org/get"
# }
```

- - verify：开关， 用于认证SSL证书， 默认为True

```
## request(method, url, **kwargs)，当 **kwargs 为 verify，SSL证书验证
import requests

r = requests.request('GET', 'https://kyfw.12306.cn/otn/', verify=True)
print(r.text)

r = requests.request('GET', 'https://kyfw.12306.cn/otn/', verify=False)
print(r.text)

r = requests.request('GET', 'https://github.com', verify=True)
print(r.text)
```

- - allow_redirects: 开关， 表示是否允许对url进行重定向， 默认为True。
- - stream: 开关， 指是否对获取内容进行立即下载， 默认为True。
- - cert： 用于设置保存本地SSL证书路径

### requests.get(url, params=None, **kwargs)

```
# 官方文档
def get(url, params=None, **kwargs):
    kwargs.setdefault('allow_redirects', True)
    return request('get', url, params=params, **kwargs)
```

### requests.post(url, data=None, json=None, **kwargs)

```
# 官方文档
def post(url, data=None, json=None, **kwargs):
    return request('post', url, data=data, json=json, **kwargs)
```

### requests.head(url, **kwargs)

```
# 官方文档
def head(url, **kwargs):
    kwargs.setdefault('allow_redirects', False)
    return request('head', url, **kwargs)
```

### requests.options(url, **kwargs)

```
# 官方文档
def options(url, **kwargs):
    kwargs.setdefault('allow_redirects', True)
    return request('options', url, **kwargs)
```

### requests.put(url, data=None, **kwargs)

```
# 官方文档
def put(url, data=None, **kwargs):
    return request('put', url, data=data, **kwargs)
```

### requests.patch(url, data=None, **kwargs)

```
# 官方文档
def patch(url, data=None, **kwargs):
    return request('patch', url, data=data, **kwargs)
```

> requests.patch和request.put类似。
>  两者不同的是： 当我们用patch时仅需要提交需要修改的字段。
>  而用put时，必须将所有字段一起提交到url，未提交字段将会被删除。
>  patch的好处是：节省网络带宽。

### requests.delete(url, **kwargs)

```
# 官方文档
def delete(url, **kwargs):
    return request('delete', url, **kwargs)
```

### requests库的异常

注意requests库有时会产生异常，比如网络连接错误、http错误异常、重定向异常、请求url超时异常等等。所以我们需要判断r.status_codes是否是200，在这里我们怎么样去捕捉异常呢？
 这里我们可以利用r.raise_for_status() 语句去捕捉异常，该语句在方法内部判断r.status_code是否等于200，如果不等于，则抛出异常。
 于是在这里我们有一个爬取网页的通用代码框架

```
try:
    r = requests.get(url, timeout=30)  # 请求超时时间为30秒
    r.raise_for_status()  # 如果状态不是200，则引发异常
    r.encoding = r.apparent_encoding  # 配置编码
    print(r.text)
except:
    print("产生异常")
```

# 三、requests的综合小实例

### 实例一：京东商品信息的爬取

```
## 京东商品信息的爬取
# 不需要对头部做任何修改，即可爬网页
import requests

url = 'http://item.jd.com/2967929.html'
try:
    r = requests.get(url, timeout=30)
    r.raise_for_status()
    r.encoding = r.apparent_encoding
    print(r.text[:1000])  # 部分信息
except:
    print("失败")
```

### 实例二：亚马逊商品信息的爬取

```
## 亚马逊商品信息的爬取
# 该网页中对爬虫进行的爬取做了限制，因此我们需要伪装自己为浏览器发出的请求
import requests

url = 'http://www.amazon.cn/gp/product/B01M8L5Z3Y'
try:
    kv = {'user_agent': 'Mozilla/5.0'}
    r = requests.get(url, headers=kv)  # 改变自己的请求数据
    r.raise_for_status()
    r.encoding = r.apparent_encoding
    print(r.text[1000:2000])  # 部分信息
except:
    print("失败")
```

### 实例三：百度搜索关键字提交

```
## 百度搜索关键字提交
# 百度的关键字接口：https://www.baidu.com/s?wd=keyword
import requests

keyword = 'python'
try:
    kv = {'wd': keyword}
    headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36"}
    r = requests.get('https://www.baidu.com/s', params=kv, headers=headers)
    r.raise_for_status()
    r.encoding = r.apparent_encoding
    # print(len(r.text))
    print(r.text)
except:
    print("失败")
```

### 实例四：网络图片的爬取

```
## 网络图片的爬取
import requests
import os

try:
    url = "https://odonohz90.qnssl.com/library/145456/bb0b3faa7a872d012bb4c57256b47585.jpg?imageView2/2/w/1000/h/1000/q/75"  # 图片地址
    root = r"D:\DataguruPyhton\PythonSpider\lesson3\pic\\"
    path = root + url.split("/")[-1]
    if not path.endswith(".jpg"):
        path += ".jpg"
    if not os.path.exists(root):  # 目录不存在创建目录
        os.mkdir(root)
    if not os.path.exists(path):  # 文件不存在则下载
        r = requests.get(url)
        f = open(path, "wb")
        f.write(r.content)
        f.close()
        print("文件下载成功")
    else:
        print("文件已经存在")
except:
    print("获取失败")
```

