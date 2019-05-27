---
title: 什么是 Shodan？
date: 2019-05-17 09:25:00
author: Demo
img: /source/images/xxx.jpg
top: true
cover: true
coverImg: /images/1.jpg
toc: false
mathjax: false
summary: 
categories: Shodan
tags:
  - Shodan

---

## 什么是 Shodan？

首先，Shodan 是一个搜索引擎，但它与 Google 这种搜索网址的搜索引擎不同，Shodan 是用来搜索网络空间中在线设备的，你可以通过 Shodan 搜索指定的设备，或者搜索特定类型的设备，其中 Shodan 上最受欢迎的搜索内容是：webcam，linksys，cisco，netgear，SCADA等等。

**那么 Shodan 是怎么工作的呢？Shodan 通过扫描全网设备并抓取解析各个设备返回的 banner 信息，通过了解这些信息 Shodan 就能得知网络中哪一种 Web 服务器是最受欢迎的，或是网络中到底存在多少可匿名登录的 FTP 服务器。**

## 基本用法

这里就像是用 Google 一样，在主页的搜索框中输入想要搜索的内容即可，例如下面我搜索 “SSH”：

[![27-1.png](http://image.3001.net/images/20161128/14803149659337.png!small)](http://image.3001.net/images/20161128/14803149659337.png)

上图的搜索结果包含两个部分，左侧是大量的汇总数据包括：

- Results map – 搜索结果展示地图
- Top services (Ports) – 使用最多的服务/端口
- Top organizations (ISPs) – 使用最多的组织/ISP
- Top operating systems – 使用最多的操作系统
- Top products (Software name) – 使用最多的产品/软件名称

随后，在中间的主页面我们可以看到包含如下的搜索结果：

- IP 地址
- 主机名
- ISP
- 该条目的收录收录时间
- 该主机位于的国家
- Banner 信息

想要了解每个条目的具体信息，只需要点击每个条目下方的 details 按钮即可。此时，URL 会变成这种格式 `https://www.shodan.io/host/[IP]`，所以我们也可以通过直接访问指定的 IP 来查看详细信息。

[![27-2.png](http://image.3001.net/images/20161128/14803149873250.png!small)](http://image.3001.net/images/20161128/14803149873250.png)

上图中我们可以从顶部在地图中看到主机的物理地址，从左侧了解到主机的相关信息，右侧则包含目标主机的端口列表及其详细信息。

### 使用搜索过滤

如果像前面单纯只使用关键字直接进行搜索，搜索结果可能不尽人意，那么此时我们就需要使用一些特定的命令对搜索结果进行过滤，常见用的过滤命令如下所示：

- `hostname`：搜索指定的主机或域名，例如 `hostname:"google"`
- `port`：搜索指定的端口或服务，例如 `port:"21"`
- `country`：搜索指定的国家，例如 `country:"CN"`
- `city`：搜索指定的城市，例如 `city:"Hefei"`
- `org`：搜索指定的组织或公司，例如 `org:"google"`
- `isp`：搜索指定的ISP供应商，例如 `isp:"China Telecom"`
- `product`：搜索指定的操作系统/软件/平台，例如 `product:"Apache httpd"`
- `version`：搜索指定的软件版本，例如 `version:"1.6.2"`
- `geo`：搜索指定的地理位置，参数为经纬度，例如 `geo:"31.8639, 117.2808"`
- `before/after`：搜索指定收录时间前后的数据，格式为dd-mm-yy，例如 `before:"11-11-15"`
- `net`：搜索指定的IP地址或子网，例如 `net:"210.45.240.0/24"`

### 搜索实例

查找位于合肥的 Apache 服务器：

```
apache city:"Hefei"
```

查找位于国内的 Nginx 服务器：

```
nginx country:"CN"
```

查找 GWS(Google Web Server) 服务器：

```
"Server: gws" hostname:"google"
```

查找指定网段的华为设备：

```
huawei net:"61.191.146.0/24"
```

如上通过在基本关键字后增加指定的过滤关键字，能快速的帮助发现我们感兴趣的内容。当然，还有更快速更有意思的方法，那就是点击 Shodan 搜索栏右侧的 “Explore” 按钮，就会得到很多别人分享的搜索语法，你问我别人分享的语法有什么好玩的？那咱们就随便来看看吧：

[![27-3.png](http://image.3001.net/images/20161128/14803150204500.png!small)](http://image.3001.net/images/20161128/14803150204500.png)

咱们随便选取一个名为“NetSureveillance Web”的用户分享语法，从下面的描述信息我们基本就能得知这就是一个弱密码的漏洞，为了方便测试让我们把语法在增加一个国家的过滤信息，最终语法如下：

```
Server: uc-httpd 1.0.0 200 OK Country:"CN"
```

[![27-4.png](http://image.3001.net/images/20161128/14803150355281.png!small)](http://image.3001.net/images/20161128/14803150355281.png)

现在让我们随便选取一个页面进去输入，使用admin账号和空密码就能顺利进入了：）

[![27-5.png](http://image.3001.net/images/20161128/1480315053949.png!small)](http://image.3001.net/images/20161128/1480315053949.png)

### 其他功能

Shodan 不仅可以查找网络设备，它还具有其他相当不错的功能。

Exploits：每次查询完后，点击页面上的 “Exploits” 按钮，Shodan 就会帮我们查找针对不同平台、不同类型可利用的 exploits。当然也可以通过直接访问网址来自行搜索：<https://exploits.shodan.io/welcome>；

[![27-10.png](http://image.3001.net/images/20161128/14803151039105.png!small)](http://image.3001.net/images/20161128/14803151039105.png)

地图：每次查询完后，点击页面上的 “Maps” 按钮，Shodan 会将查询结果可视化的展示在地图当中；

[![27-12.png](http://image.3001.net/images/20161128/14803151185705.png!small)](http://image.3001.net/images/20161128/14803151185705.png)

报表：每次查询完后，点击页面上的 “Create Report” 按钮，Shodan 就会帮我们生成一份精美的报表，这是天天要写文档兄弟的一大好帮手啊；

[![27-11.png](http://image.3001.net/images/20161128/14803151323230.png!small)](http://image.3001.net/images/20161128/14803151323230.png)

## 命令行下使用 Shodan

`Shodan` 是由官方提供的 Python 库的，项目位于：<https://github.com/achillean/shodan-python>

**安装**

```
pip install shodan
```

或者

```
git clone https://github.com/achillean/shodan-python.git && cd shodan-python
python setup.py install
```

安装完后我们先看下帮助信息：

```
➜  ~ shodan -h
Usage: shodan [OPTIONS] COMMAND [ARGS]...
Options:
  -h, --help  Show this message and exit.
Commands:
  alert       Manage the network alerts for your account  # 管理账户的网络提示
  convert     Convert the given input data file into a...  # 转换输入文件
  count       Returns the number of results for a search  # 返回查询结果数量
  download    Download search results and save them in a...  # 下载查询结果到文件
  honeyscore  Check whether the IP is a honeypot or not.  # 检查 IP 是否为蜜罐
  host        View all available information for an IP...  # 显示一个 IP 所有可用的详细信息
  info        Shows general information about your account  # 显示账户的一般信息
  init        Initialize the Shodan command-line  # 初始化命令行
  myip        Print your external IP address  # 输出用户当前公网IP
  parse       Extract information out of compressed JSON...  # 解析提取压缩的JSON信息，即使用download下载的数据
  scan        Scan an IP/ netblock using Shodan.  # 使用 Shodan 扫描一个IP或者网段
  search      Search the Shodan database  # 查询 Shodan 数据库
  stats       Provide summary information about a search...  # 提供搜索结果的概要信息
  stream      Stream data in real-time.  # 实时显示流数据
```

### 常用示例

**init**

初始化命令行工具。

```
➜  ~ shodan init [API_Key]
Successfully initialized
```

**count** 

返回查询的结果数量。

```
➜  ~ shodan count microsoft iis 6.0
575862
```

**download** 

将搜索结果下载到一个文件中，文件中的每一行都是 JSON 格式存储的目标 banner 信息。默认情况下，该命令只会下载1000条结果，如果想下载更多结果需要增加 `--limit` 参数。

[![27-6.png](http://image.3001.net/images/20161128/14803151703857.png!small)](http://image.3001.net/images/20161128/14803151703857.png)

**parse**

我们可以使用 parse 来解析之前下载数据，它可以帮助我们过滤出自己感兴趣的内容，也可以用来将下载的数据格式从 JSON 转换成 CSV 等等其他格式，当然更可以用作传递给其他处理脚本的管道。例如，我们想将上面下载的数据以CSV格式输出IP地址、端口号和组织名称：

```
➜  ~ shodan parse --fields ip_str,port,org --separator , microsoft-data.json.gz
```

[![27-7.png](http://image.3001.net/images/20161128/1480315189686.png!small)](http://image.3001.net/images/20161128/1480315189686.png)

**host**

查看指定主机的相关信息，如地理位置信息，开放端口，甚至是否存在某些漏洞等信息。

[![27-8.png](http://image.3001.net/images/20161128/14803152025933.png!small)](http://image.3001.net/images/20161128/14803152025933.png)

**search**

直接将查询结果展示在命令行中，默认情况下只显示IP、端口号、主机名和HTTP数据。当然我们也可以通过使用 –fields 来自定义显示内容，例如，我们只显示IP、端口号、组织名称和主机名：

```
➜  ~ shodan search --fields ip_str,port,org,hostnames microsoft iis 6.0
```

[![27-9.png](http://image.3001.net/images/20161128/14803152181807.png!small)](http://image.3001.net/images/20161128/14803152181807.png)

## 代码中使用 Shodan 库

还是使用上一节讲到的 [`shodan`](https://github.com/achillean/shodan-python) 库，安装方式这里不在阐述了。同样的，在使用 `shodan` 库之前需要初始化连接 API，代码如下：

```
import shodan
SHODAN_API_KEY = "API_Key"
api = shodan.Shodan(SHODAN_API_KEY)
```

随后，我们就可以搜索数据了，示例代码片如下：

```
try:
    # 搜索 Shodan
    results = api.search('apache')
    # 显示结果
    print 'Results found: %s' % results['total']
    for result in results['matches']:
            print result['ip_str']
except shodan.APIError, e:
    print 'Error: %s' % e
```

[![27-13.png](http://image.3001.net/images/20161128/14803152351577.png!small)](http://image.3001.net/images/20161128/14803152351577.png)

这里 `Shodan.search()` 会返回类似如下格式的 JSON 数据：

```
{
        'total': 8669969,
        'matches': [
                {
                        'data': 'HTTP/1.0 200 OK\r\nDate: Mon, 08 Nov 2010 05:09:59 GMT\r\nSer...',
                        'hostnames': ['pl4t1n.de'],
                        'ip': 3579573318,
                        'ip_str': '89.110.147.239',
                        'os': 'FreeBSD 4.4',
                        'port': 80,
                        'timestamp': '2014-01-15T05:49:56.283713'
                },
                ...
        ]
}
```

### 常用 Shodan 库函数

- `shodan.Shodan(key)` ：初始化连接API
- `Shodan.count(query, facets=None)`：返回查询结果数量
- `Shodan.host(ip, history=False)`：返回一个IP的详细信息
- `Shodan.ports()`：返回Shodan可查询的端口号
- `Shodan.protocols()`：返回Shodan可查询的协议
- `Shodan.services()`：返回Shodan可查询的服务
- `Shodan.queries(page=1, sort='timestamp', order='desc')`：查询其他用户分享的查询规则
- `Shodan.scan(ips, force=False)`：使用Shodan进行扫描，ips可以为字符或字典类型
- `Shodan.search(query, page=1, limit=None, offset=None, facets=None, minify=True)`：查询Shodan数据