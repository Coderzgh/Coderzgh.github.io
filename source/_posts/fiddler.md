---
title: 使用fiddler对手机上的程序进行抓包
date: 2019-06-02 09:25:00
author: Demo
img: /source/images/xxx.jpg
top: true
cover: true
coverImg: /images/1.jpg
toc: false
mathjax: false
summary: 
categories: fiddler
tags:
  - fiddler

---

- ### 用fiddler对手机上的程序进行抓包，网上有很多的资料，这里写一下来进行备用。

   

  #### **前提：**

  1.必须确保安装fiddler的电脑和手机在同一个wifi环境下

  备注：如果电脑用的是台式机，可以安装一个随身wifi，来确保台式机和手机在同一wifi环境下

  2.可以使用电脑安卓模拟器

   

  #### **安装配置步骤：**

  ##### 1.下载一个fiddler，网上随便下一个就可以了

  ##### 2.配置fiddler

  **Tools->Fiddler Options->Connections**

  **![img](https://images2015.cnblogs.com/blog/626983/201511/626983-20151126120716187-103213269.png)**

  说明：1.Fiddler listens on port是手机连接fiddler时的代理端口号，默认8888即可

  ​          2.Allow remote computers to connect是允许远程发送请求，需要勾上

   

  **Tools->Fiddler Options->HTTPS**
  
  **![img](https://images2015.cnblogs.com/blog/626983/201511/626983-20151126120733937-1858884840.png)**



  说明：勾上Decrypt HTTPS traffic，会抓到手机的https请求，如果想抓到https请求还需要在手机安装证书，下面会介绍

  【fiddler设置后一定要把fiddler重启一下才会生效】

   

#####   3.手机上的配置

  3.1需要安装fiddler证书

  使用手机浏览器访问http://【电脑IP地址】:【fiddler设置的端口号】，既可以下载fiddler的证书并安装

  【查看电脑IP的方法，直接在cmd下ipconfig，或者鼠标滑过fiddler的online也可以看到IP地址】

  ![img](https://images2015.cnblogs.com/blog/626983/201511/626983-20151126120802296-757506898.png)

  以上面看到的我的IP地址为例，手机只要访问http://10.252.167.91:8888即可下载安装fiddler证书

   

  3.2手机设置wifi的代理

  连接与电脑相同的wifi，修改wifi的网络，手动设置代理，代理服务器主机名为电脑的IP地址，代理端口为在fiddler里设置的端口号，保存后，fiddler将能够收到手机上的请求信息

  ![img](https://images2015.cnblogs.com/blog/626983/201511/626983-20151126120829921-223800751.png)

   

  以上就是配置方法，其他的就可以直接用了，比如在fiddler里进行一下请求的过滤，只看某个服务器下的请求，配置后要点一下Actions来保存过滤

  ![img](https://images2015.cnblogs.com/blog/626983/201511/626983-20151126120845093-525900958.png)

  在测试中可能会有测试环境，测试环境有的公司时域名相同，但是hosts不同，通过不同的服务器IP地址指向来确定是什么环境。在PC测试上可以非常方便的更改本机hosts指向来切换测试环境和线上环境，在手机上更改hosts比较麻烦。这时候就可以利用fiddler来连接手机，更改电脑的hosts，来实现手机连接测试环境的操作。

   

  注意：

  1.手机配置了代理，fiddler必须启动，手机才可以上网，如果fiddler关闭后手机是不可以联网了，需要将代理去掉才可以进行联网。

  2.fiddler启东时，会默认将Internet的代理更改为127.0.0.1，在正常退出fiddler时代理会恢复为原来的代理。但是如果遇到fiddler不正常退出（比如进程直接杀掉），会导致代理没有恢复的情况，这是需要手动修改Internet的代理（恢复为原来的代理或者取消代理）

  设置Internet代理的方法如下：

  ![img](https://images2015.cnblogs.com/blog/626983/201511/626983-20151126120853421-360569062.png)

   