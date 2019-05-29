---
title: Python网络爬虫实战之七：动态网页爬取案例实战 Selenium + PhantomJS
date: 2019-05-19 09:25:00
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
  - Selenium
  - PhantomJS

---

- ## 正文：

  ## 一、Selenium

  ### 1、Selenium是什么

  Selenium 是什么？一句话，自动化测试工具。它支持各种浏览器，包括 Chrome，Safari，Firefox 等主流界面式浏览器，如果你在这些浏览器里面安装一个 Selenium 的插件，那么便可以方便地实现Web界面的测试。换句话说叫 Selenium 支持这些浏览器驱动。

  ### 2、安装Selenium

  - 方法一：pip install selenium
  - 方法二：[下载源码](https://pypi.org/simple/selenium/)后解压，进入解压后的目录，执行 python setup.py install

  ### 3、安装浏览器驱动

  使用谷歌浏览器，则下载[chromedriver.exe](https://sites.google.com/a/chromium.org/chromedriver/downloads)，下载成功后，把chromedriver.exe复制到Python安装路径下的Scripts目录中

  使用其他浏览器方法类似

  ### 4、快速体验Selenium

  使用pyhon打开浏览器，并自动访问百度首页

  ```
  from selenium import webdriver
  
  browser = webdriver.Chrome()
  browser.get('http://www.baidu.com/')
  ```

  打开浏览器，并自动在百度中搜索“Python”关键词

  ```
  from selenium import webdriver
  from selenium.webdriver.common.keys import Keys
  
  browser = webdriver.Chrome()
  browser.get('https://www.baidu.com')
  input = browser.find_element_by_id('kw')
  input.send_keys('Python')
  input.send_keys(Keys.ENTER)
  ```

  ### 5、爬取网页代码：自动在百度中搜索“Python”关键词后的网页

  ```
  from selenium import webdriver
  from selenium.webdriver.common.by import By
  from selenium.webdriver.common.keys import Keys
  from selenium.webdriver.support import expected_conditions as EC
  from selenium.webdriver.support.wait import WebDriverWait
  
  browser = webdriver.Chrome()
  try:
      browser.get('https://www.baidu.com')
      input = browser.find_element_by_id('kw')
      input.send_keys('Python')
      input.send_keys(Keys.ENTER)
      wait = WebDriverWait(browser, 10)
      wait.until(EC.presence_of_element_located((By.ID, 'content_left')))
      print(browser.current_url)
      print(browser.get_cookies())
      print(browser.page_source)
  finally:
      browser.close()
  ```

  Selenium的更详细资料可参考[Selenium with Python中文翻译文档](http://selenium-python-zh.readthedocs.io/en/latest/index.html)

  ## 二、PhantomJS

  ### 1、PhantomJs是什么？

  PhantomJs是什么？是一个基于Webkit的"无界面"(headless)浏览器，它会把网站加载到内存并执行页面上的JavaScript，因为不会展示图形界面，所以运行起来比完整的浏览器更高效。

  爬取动态网页的时候，通常是PhantomJS 用来渲染解析JS，Selenium 用来驱动以及与 Python 的对接，Python 进行后期的处理，完美的三剑客！

  ### 2、安装PhantomJS

  [PhantomJS官方下载地址](http://phantomjs.org/download.html)
   下载成功后，解压得到phantomjs-2.1.1-windows文件夹，bin目录下的phantomjs.exe文件才是我们真正需要的，example目录下的文件时官方示例代码。

  可以通过设置环境变量来使用PhantomJS，也可以通过指定路径来操作。设置环境变量的方法式把phantomjs-2.1.1-windows的bin配置到path中，比如我的路径是D:\python\PythonLibs\phantomjs-2.1.1-windows\bin

  通过输出PhantomJS来检验PhantomJS是否可用：

  - 如果配置了系统环境变量，在cmd控制台直接输入：phantomjs -v
  - 如果没有配置系统全局变量，则进入到phantomjs.exe文件的所在目录，比如：D:\Python\PythonLibs\phantomjs-2.1.1-windows\bin，然后再执行 phantomjs -v

  ### 3、快速体验PhantomJS

  使用方法：进入phantomjs.exe所在目录，然后执行 phantomjs 文件绝对路径

  执行官方示例中的hello.js文件（假设没有配置系统全局变量）

  ```
  D:\DataguruPyhton>cd D:\python\PythonLibs\phantomjs-2.1.1-windows\bin
  D:\Python\PythonLibs\phantomjs-2.1.1-windows\bin>phantomjs D:\python\PythonLibs\pha
  ntomjs-2.1.1-windows\examples\hello.js
  
  运行结果是：Hello, world!
  ```

  其中hello.js中的代码是：

  ```
  "use strict";
  console.log('Hello, world!');
  phantom.exit();
  ```

  执行官方示例中的version.js文件（假设没有配置系统全局变量）

  ```
  D:\Python\PythonLibs\phantomjs-2.1.1-windows\bin>phantomjs D:\python\PythonLibs\pha
  ntomjs-2.1.1-windows\examples\version.js
  
  运行结果是：using PhantomJS version 2.1.1
  ```

  其中version.js中的代码是：

  ```
  "use strict";
  console.log('using PhantomJS version ' +
    phantom.version.major + '.' +
    phantom.version.minor + '.' +
    phantom.version.patch);
  phantom.exit();
  ```

  PhantomJS更详细资料可参考[PhantomJS官方文档](http://phantomjs.org/documentation/)

  ## 三、Selenium+PhantomJS结合使用

  Selenium + PhantomJS 实例一

  ```
  from selenium import webdriver
  # 调用键盘按键操作需要引入keys包
  from selenium.webdriver.common.keys import Keys
  
  # 调用指定的PhantomJS浏览器创建浏览器对象（没有在环境变量中指定PhantomJS位置）
  driver = webdriver.PhantomJS(executable_path=r'D:\python\PythonLibs\phantomjs-2.1.1-windows\bin\phantomjs')
  # 调用环境变量指定的PhantomJS浏览器创建浏览器对象（如果已经在环境变量中指定了PhantomJS位置）
  # driver = webdriver.PhantomJS()
  driver.set_window_size(1366, 768)
  # get方法会一直等到页面加载，然后才会继续程序，通常测试会在这里选择time.sleep(2)
  driver.get("http://www.baidu.com/")
  # 获取页面名为wraper的id标签的文本内容
  data = driver.find_element_by_id('wrapper').text
  # 打印数据内容
  print(data)
  # 把百度设为主页关于百度About  Baidu百度推广
  # ©2018 Baidu 使用百度前必读 意见反馈 京ICP证030173号  京公网安备11000002000001号
  print(driver.title)  # result: 百度一下，你就知道
  # 生成页面快照并保存
  driver.save_screenshot(r'D:\DataguruPyhton\PythonSpider\images\baidu.png')
  # id="kw"是百度搜索输入框，输入字符串"长城"
  driver.find_element_by_id('kw').send_keys(u'长城')
  # id="su"是百度搜索按钮，click()是模拟点击
  driver.find_element_by_id('su').click()
  # 获取新的页面快照
  driver.save_screenshot(r'D:\DataguruPyhton\PythonSpider\images\长城.png')
  # 打印网页渲染后的源代码
  print(driver.page_source)
  # 获取当前页面Cookie
  print(driver.get_cookies())
  driver.quit()
  ```

  Selenium + PhantomJS 实例二

  ```
  from selenium import webdriver
  import time
  # 调用键盘按键操作需要引入keys包
  from selenium.webdriver.common.keys import Keys
  
  # 调用指定的PhantomJS浏览器创建浏览器对象（没有在环境变量中指定PhantomJS位置）
  driver = webdriver.PhantomJS(executable_path=r'D:\python\PythonLibs\phantomjs-2.1.1-windows\bin\phantomjs')
  # 调用环境变量指定的PhantomJS浏览器创建浏览器对象（如果已经在环境变量中指定了PhantomJS位置）
  # driver = webdriver.PhantomJS()
  driver.set_window_size(1366, 768)
  # get方法会一直等到页面加载，然后才会继续程序，通常测试会在这里选择time.sleep(2)
  driver.get("http://www.baidu.com/")
  # id="kw"是百度搜索输入框，输入字符串"情人节"
  driver.find_element_by_id('kw').send_keys(u'情人节')
  # ctrl+a全选输入框内容
  driver.find_element_by_id('kw').send_keys(Keys.CONTROL, 'a')
  # ctrl+x剪切输入框内容
  driver.find_element_by_id('kw').send_keys(Keys.CONTROL, 'x')
  # 输入框重新输入内容
  driver.find_element_by_id('kw').send_keys('鲜花')
  # 模拟Enter回车键
  driver.find_element_by_id('su').send_keys(Keys.RETURN)
  time.sleep(5)
  # 清空输入框内容
  driver.find_element_by_id('kw').clear()
  # 生成新的页面快照
  driver.save_screenshot(r'D:\DataguruPyhton\PythonSpider\images\鲜花.png')
  # 获取当前url
  print(driver.current_url)
  driver.quit()
  ```

  Selenium + PhantomJS 实例三：爬取包含Ajax的动态网页数据

  ```
  ## Selenium+PhantomJS爬取包含Ajax的动态网页数据：通过手动延时
  from selenium import webdriver
  import time
  
  driver = webdriver.PhantomJS(executable_path=r'D:\python\PythonLibs\phantomjs-2.1.1-windows\bin\phantomjs')
  driver.get("http://pythonscraping.com/pages/javascript/ajaxDemo.html")
  # driver.page_source
  time.sleep(3)
  print(driver.find_element_by_id("content").text)
  driver.close()
  ```

  完善后的代码

  ```
  ## Selenium+PhantomJS爬取包含Ajax的动态网页数据：通过检查页面是否加载完毕
  from selenium.webdriver.common.by import By
  from selenium.webdriver.support.ui import WebDriverWait
  from selenium.webdriver.support import expected_conditions as EC
  
  driver = webdriver.PhantomJS(executable_path=r'D:\python\PythonLibs\phantomjs-2.1.1-windows\bin\phantomjs')
  driver.get("http://pythonscraping.com/pages/javascript/ajaxDemo.html")
  try:
      element = WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.ID, "loadedButton")))
  finally:
      print(driver.find_element_by_id("content").text)
      driver.close()
  ```

  Selenium + PhantomJS 实例四：爬取重定向的动态网页数据

  ```
  from selenium import webdriver
  from selenium.common.exceptions import StaleElementReferenceException
  
  
  def waitForLoad(driver):
      elem = driver.find_element_by_tag_name("html")
      count = 0
      while True:
          count += 1
          if count > 20:
              print("Timing out after 10 seconds and returning")
              return
          time.sleep(.5)
          try:
              elem == driver.find_element_by_tag_name("html")
          except StaleElementReferenceException:
              return
  
  
  driver = webdriver.PhantomJS(executable_path=r'D:\python\PythonLibs\phantomjs-2.1.1-windows\bin\phantomjs')
  driver.get("http://pythonscraping.com/pages/javascript/redirectDemo1.html")
  waitForLoad(driver)
  print(driver.page_source)
  ```

  ## 四、Selenium+PhantomJS使用时报错原因及解决方案

  ### 1、现象

  报错日志：

  ```
  UserWarning: Selenium support for PhantomJS has been deprecated, please use headless versions of Chrome or Firefox instead warnings.warn('Selenium support for PhantomJS has been deprecated, please use headless '
  ```

  就是说selenium已经放弃PhantomJS，了，建议使用火狐或者谷歌无界面浏览器。

  ### 2、解决方案

  - 方案一：selenium版本降级，卸载当前selenium，重现安装低版本的selenium，比如pip install selenium==3.8.0
  - 方案二：使用 Selenium + Headless Firefox 或 Selenium + Headless Chrome

  > 将在下篇文章中详细介绍 Selenium + Headless Chrome

  ## 五、本篇文章中的代码，运行环境

  - Python 3.6.4
  - selenium 3.8.0
  - phantomjs-2.1.1-windows
  - chromedriver.exe

  ## 六、彩蛋：pillow对图片进行处理

  为以后读取图片验证码铺垫

  ```
  # 需要先安装pillow,安装方法：pip install pillow
  from PIL import Image, ImageFilter
  
  kitten = Image.open(u"D:\DataguruPyhton\PythonSpider\images\girl1.jpg")
  blurryKitten = kitten.filter(ImageFilter.GaussianBlur)
  blurryKitten.save(u"D:\DataguruPyhton\PythonSpider\images\girl2.jpg")
  blurryKitten.show()
  ```

  