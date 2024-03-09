## scrapy备忘录

### scrapy的安装

```cmd
# windows
pip install wheel
pip install Twisted
pip install pywin32
pip install scrapy
# 终端输入 scrapy 无报错表示安装成功

#---------------------------------

# ubuntu
sudo apt-get update

sudo apt-get install python-pip python-lxml python-crypto python-cssselect python-openssl python-w3lib python-twisted python-dev libxml2-dev libxslt1-dev zlib1g-dev libffi-dev libssl-dev

sudo pip install scrapy

#---------------------------------

# centOS
sudo yum update
sudo yum -y install libxslt-devel pyOpenSSL python-lxml python-devel gcc
sudo easy_install scrapy
```



### 创建工程

`scrapy startproject projectName`

![image-20221126191550837](C:\Users\hxh\Desktop\note\img\image-20221126191550837.png)

### 创建爬虫

```cmd
cd scrapyDemo
scrapy genspider example example.com
```

![image-20221126193828470](C:\Users\hxh\Desktop\note\img\image-20221126193828470.png)

### 执行爬虫程序

+ > 项目目录下 执行  ：
  >
  > `scrapy crawl example`   
  >
  > 取消日志显示 ：
  >
  > `scrapy crawl exampl --nolog`

+ > 利用python程序执行scrapy脚本
  >
  > ```python
  > from scrapy.cmdline import execute
  > 
  > def main():
  >     execute(['scrapy', 'crawl', 'example'])
  >     
  > if __name__ == "__main__":
  >     main()
  > ```

### setting.py初始化配置

```python
# 关闭君子协议
ROBOTSTXT_OBEY = False

# 设置user-agent
from fake_useragent import UserAgent

USER_AGENT = UserAgent().random

# 设置日志显示级别
LOG_LEVEL = "ERROR"

# 保存csv文件 出现这种情况 瀵掑啲瀹濈彔鎶勮鎴愬姛 鐖嗗彂浼ゅ 40涓?寮€蹇冧竴涓?
FEED_EXPORT_ENCODING = 'gb18030'

# 保存json和txt文件，出现这种东西不是乱码，是unicode
# FEED_EXPORT_ENCODING = 'utf-8'
```

### 数据解析

```python
def parse(self, response):
    # response.xpath()返回的是一个Selector对象
    list_title_ele = response.xpath("//header/h2/a/text()")
    # response.xpath().extract_first() 返回第一个Selector对象里面的data数据
    first_title = response.xpath("//header/h2/a/text()").extract_first()
    # response.xpath().extract_first() 返回所有Selector对象里面的data数据集合
    list_title = response.xpath("//header/h2/a/text()").extract()
    print(list_title_ele)
    print(first_title)
    print(list_title)
```

### 持久化存储

+ ##### 基于终端指令的持久化存储

  > 此方式只能将parse方法的返回值存储到本地文本文件中
  >
  > 保存的文件格式只能是：
  >
  > `('json', 'jsonlines', 'jsonl', 'jl', 'csv', 'xml', 'marshal', 'pickle')`
  >
  > ---
  >
  > ```python
  > list_title = response.xpath("//header/h2/a/text()").extract()
  > list_href = response.xpath("//header/h2/a/@href").extract()
  > return dict(zip(list_title, list_href))
  > ```
  >
  > 执行终端指令：`scrapy crawl example -o ./temp.json`

+ ##### 基于管道持久化存储

  > 1. 数据解析
  >
  >    > ```python
  >    > list_title = response.xpath("//header/h2/a/text()").extract()
  >    > list_href = response.xpath("//header/h2/a/@href").extract()
  >    > ```
  >
  > 2. 在item.py中定义与解析数据相关的属性
  >
  >    > ```python
  >    > class ScrapydemoItem(scrapy.Item):
  >    >     title = scrapy.Field()
  >    >     href = scrapy.Field()
  >    > ```
  >
  > 3. 在爬虫文件(xxxSpider.py)中将item对象封装后交给管道(pipelines)进行持久化存储
  >
  >    > ```python
  >    > item = ScrapydemoItem()
  >    > for i in range(len(list_title)):
  >    >     item["title"] = list_title[i]
  >    >     item["href"] = list_href[i]
  >    >     yield item
  >    > ```
  >
  > 4. 在管道类process_item方法中处理、存储接受的item对象
  >
  >    > ```python
  >    > class ScrapydemoPipeline:
  >    >     # 重写父类方法,只会在启动程序时执行一次
  >    >     def open_spider(self, spider):
  >    >         self.f = open("./temp.txt", "w", encoding="utf-8")
  >    >         print("start")
  >    > 
  >    >     # 重写父类方法,只会在结束程序时执行一次
  >    >     def close_spider(self, spider):
  >    >         self.f.close()
  >    >         print("end")
  >    > 
  >    >     # (example.py),yield一次数据,就会自动调用process_item(xxx)方法
  >    >     def process_item(self, item, spider):
  >    >         self.f.write("{}:{}\n".format(item["title"], item["href"]))
  >    >        # 一般情况下要将item返回，目的是将item封装的数据交给下一个处理的管道类对象
  >    >         return item
  >    > ```
  >
  > 5. 在配置文件中开启管道类相关配置
  >
  >    > ```python
  >    > ITEM_PIPELINES = {
  >    >     # 数值(300)越小,优先级越高
  >    >    'scrapyDemo.pipelines.ScrapydemoPipeline': 300,
  >    > }
  >    > ```

- ##### 需求: 一份数据多种保存方式

  > 1. 配置文件中开启多个管道，数值越小优先级越高
  >
  >    > ```python
  >    > ITEM_PIPELINES = {
  >    >     'scrapyDemo.pipelines.ScrapydemoPipeline': 300,
  >    >     'scrapyDemo.pipelines.MysqlDemoPipeline': 400,
  >    > }
  >    > ```
  >
  > 2. 在pipeline.py文件中创建MysqlDemoPipeline类对象 使用mysql保存数据
  >
  >    > ```python
  >    > class MysqlDemoPipeline(object):
  >    >     def open_spider(self, spider):
  >    >         import pymysql
  >    > 
  >    >         self.conn = pymysql.Connect(
  >    >             host='127.0.0.1',
  >    >             port=3306,
  >    >             user='root',
  >    >             passwd='root',
  >    >             db='python_test',
  >    >             charset='utf8')
  >    > 
  >    >         self.cursor = self.conn.cursor()
  >    > 
  >    >     def close_spider(self, spider):
  >    >         self.cursor.close()
  >    >         self.conn.close()
  >    > 
  >    >     def process_item(self, item, spider):
  >    >         import uuid
  >    >         sql = "insert into test1(id,title,href)values('{}','{}','{}')".format(str(uuid.uuid1()), item["title"],item["href"])
  >    >         try:
  >    >             self.cursor.execute(sql)
  >    >             self.conn.commit()
  >    >         except Exception as e:
  >    >             print(e)
  >    >             self.conn.rollback()
  >    >         # 一般情况下要将item返回，目的是将item封装的数据交给下一个处理的管道类对象
  >    >         return item
  >    > ```

### 五大核心组件

- ![img](https://pic.rmb.bdstatic.com/bjh/down/d28e83db92e0e94adc238cb337d073c5.jpeg)

- > __scrapy Engine__: 用来处理整个系统的数据流处理, 触发事务(框架核心)
  >
  > 
  >
  > __Scheduler__: 用来接受引擎发过来的请求, 压入队列中, 并在引擎再次请求的时候返回. 可以想像成一个URL的优先队列, 由它来决定下一个要抓取的网址是什么, 同时去除重复的网址
  >
  > 
  >
  > __Spider__:  主要用来干活的，用于从特定页面提取所需信息，也可以提取url传递到scrapyEngine继续爬取
  >
  > 
  >
  > __Pipeline__: 负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据
  >
  > 
  >
  > __Downloader Middlewares__: 位于Scrapy引擎和下载器之间的中间件，主要是处理ScrapyEnginedownloader之间的请求及响应
  >
  > 
  >
  > __Spider Middlewares__: 介于ScrapyEngine和Spider之间的中间件，主要工作是处理响应输入和请求输出

### 请求传参

- 所需要的请求的数据不在同一个页面，所以将部分解析的数据封装完成后传递到下一次请求当中

  > ```python
  > class TransferArgumentsSpider(scrapy.Spider):
  >     name = 'transferArguments'
  >     # start_urls = ['http://www.ypojie.com/page/2']
  >     start_urls = ['http://www.ypojie.com/page/{}'.format(i) for i in range(2, 10)]
  > 
  >     def parse(self, response):
  >         list_title = response.xpath("//header/h2/a/text()").extract()
  >         list_href = response.xpath("//header/h2/a/@href").extract()
  >         item = ScrapydemoItem()
  >         for i in range(len(list_title)):
  >             item["title"] = list_title[i]
  > 
  >             # meta 可以将封装的数据传递给下一次请求(callback对应的函数)返回的响应对象中 response.meta
  >             yield scrapy.Request(list_href[i], callback=self.parse_detail, meta={"item": item})
  > 
  >     def parse_detail(self, response):
  >         item = response.meta['item']
  >         # xpath表达式 可以通过 string(...)提取整个标签下(包含子标签)所有文本,不需要加/text()指定提取文本
  >         item["article"] = response.xpath("string(//article/p[1])").extract_first()
  >         yield item
  > ```
  >
  > 

### 图片下载

> __注意：使用scrapy中的ImagesPIpeline批量下载图片时先安装如下库，否则相应的pipeline无法获取数据__
>
> ```python
> pip install pillow
> pip install image
> ```

> 1. 编写item.py
>
>    > ```python
>    > class ImageItem(scrapy.Item):
>    >     title = scrapy.Field()
>    >     src = scrapy.Field()
>    > ```
>
> 2. 编写Spider
>
>    > ```python
>    > class DownloadImageSpider(scrapy.Spider):
>    >     name = 'downloadImage'
>    >     start_urls = ['http://pic.netbian.com/4kmeinv/index.html']
>    > 
>    >     def parse(self, response):
>    >         list_img_detail = response.xpath("//ul[@class='clearfix']//a/@href").extract()
>    >         list_img_name = response.xpath("//ul[@class='clearfix']//b/text()").extract()
>    > 
>    >         next_page_url = response.xpath("//div[@class='page']//a[last()]/@href").extract_first()
>    > 
>    >         for i in range(len(list_img_name)):
>    >             """
>    >            list_img_detail[0]=/tupian/30600.html
>    >             http://pic.netbian.com/tupian/30600.html
>    >             """
>    >             item = ImageItem()
>    >             item["title"] = list_img_name[i]
>    >             url = "http://pic.netbian.com{}".format(list_img_detail[i])
>    >             yield scrapy.Request(url, callback=self.parse_detail, meta={"item": item})
>    > 
>    >         if next_page_url is not None:
>    >             url = "http://pic.netbian.com{}".format(next_page_url)
>    >             yield scrapy.Request(url, callback=self.parse)
>    > 
>    >     def parse_detail(self, response):
>    >         item = response.meta["item"]
>    >         img_src = response.xpath("//div[@class='photo-pic']//img/@src").extract_first()
>    >         """
>    >         img_src=/uploads/allimg/221126/004100-1669394460ffcd.jpg
>    >         http://pic.netbian.com/uploads/allimg/221126/004100-1669394460ffcd.jpg
>    >         """
>    > 
>    >         item["src"] = "http://pic.netbian.com{}".format(img_src)
>    >         yield item
>    > ```
>
> 3. 编写pipeline.py
>
>    > ```python
>    > import scrapy
>    > from scrapy.pipelines.images import ImagesPipeline
>    > 
>    > 
>    > class DownloadImagePipeline(ImagesPipeline):
>    > 
>    >     # 利用item里面的src属性发送请求
>    >     def get_media_requests(self, item, info):
>    >         # 可以自定义meta数据  将所需要的图片名称信息传递给 def file_path(xx)
>    >         yield scrapy.Request(item["src"], meta={"item": item})
>    > 
>    >     # 设置图片文件名
>    >     def file_path(self, request, response=None, info=None, *, item=None):
>    >         # 可以通过request.meta 拿到上面传递的meta数据信息
>    >         title = request.meta["item"]["title"].replace(" ", "")
>    >         extend_name = request.url.split(".")[-1]
>    >         filename = title + "." + extend_name
>    >         print("{}下载完成".format(filename))
>    >         # 必须返回文件名
>    >         return filename
>    > 
>    >     #
>    >     def item_completed(self, results, item, info):
>    >         # return item # 将图片对象交给下一个pipeline 无特殊情况不需要这样做
>    >         pass
>    > ```
>
> 4. 编写settings.ps
>
>    > ```python
>    > BOT_NAME = 'scrapyDemo'
>    > 
>    > SPIDER_MODULES = ['scrapyDemo.spiders']
>    > NEWSPIDER_MODULE = 'scrapyDemo.spiders'
>    > ITEM_PIPELINES = {
>    >     # 'scrapyDemo.pipelines.ScrapydemoPipeline': 300,
>    >     # 'scrapyDemo.pipelines.MysqlDemoPipeline': 400,
>    >     # 'scrapyDemo.pipelines.TransferArgumentsPipeline': 500,
>    >     # 'scrapyDemo.pipelines.TestPipeline': 600,
>    >     'scrapyDemo.pipelines.DownloadImagePipeline': 100,
>    > }
>    > 
>    > # 关闭君子协议
>    > ROBOTSTXT_OBEY = False
>    > 
>    > # 设置user-agent
>    > from fake_useragent import UserAgent
>    > 
>    > USER_AGENT = UserAgent().random
>    > 
>    > # 设置日志显示级别
>    > LOG_LEVEL = "ERROR"
>    > 
>    > # 设置请求并发数量
>    > CONCURRENT_REQUESTS = 16
>    > 
>    > # 设置延迟下载时间
>    > DOWNLOAD_DELAY = 1
>    > 
>    > # 使用ImagePipeline时设置图片的保存目录
>    > IMAGES_STORE = './imgs'
>    > ```



> + __可以使用wget库下载图片__
>
> > ```python
> > import wget
> > 
> > url = "https://pic.netbian.com/uploads/allimg/220430/002435-16512494757fe7.jpg"
> > wget.download(url, out="./temp.jpg")
> > ```
>
> + __response.body也可以获取二级制文件__
>
> > ```python
> > class MiddlewaresdemoSpider(scrapy.Spider):
> >     name = 'middlewaresDemo'
> >     start_urls = ['https://pic.netbian.com/uploads/allimg/220430/002435-16512494757fe7.jpg']
> > 
> >     def parse(self, response):
> >         with open("./a.jpg", "wb")as f:
> >             f.write(response.body)
> > ```
> >
> > 

### 下载中间件

> __downloadMiddlewa__: 位于引擎和下载器之间，可以批量拦截请求和响应，可以作用于ua伪装、ip代理、篡改响应对象、数据
>
> > 在settings.py文件中配置中间件
> >
> > ```python
> > DOWNLOADER_MIDDLEWARES = {
> >    'scrapyDemo.middlewares.ScrapydemoDownloaderMiddleware': 543,
> > }
> > ```
>
> > 下载中间件位于middlewa.py
> >
> > ```python
> > class ScrapydemoDownloaderMiddleware:
> > 
> >     def process_request(self, request, spider):
> >         return None
> > 
> >     def process_response(self, request, response, spider):
> >         return response
> > 
> >     def process_exception(self, request, exception, spider):
> >         pass
> > ```
>
> - 拦截请求
>
>   > ```python
>   > # 拦截请求对象 return None表示请求继续执行
>   > def process_request(self, request, spider):
>   >     # 每次请求随机更换ua,增加隐匿性
>   >     ua = UserAgent().random
>   >     request.headers["user-agent"] = "hxh"
>   >     return None
>   > ```
>
> - 更换代理IP
>
>   > ```python
>   > # 请求异常无法处理时进去该方法
>   > def process_exception(self, request, exception, spider):
>   >     # 请求异常时更换ip代理
>   >     protocol_name = request.url.split(":")[0]
>   >     if protocol_name == "http":
>   >         request.meta["proxy"] = "http://ip:port"
>   >     else:
>   >         request.meta["proxy"] = "https://ip:port"
>   > 
>   >         # 更换异常请求对象的ip,然后返回继续执行
>   >     return request
>   > ```
>
> - 修改响应内容
>
>   > ```python
>   > from scrapy.http import HtmlResponse
>   > def process_response(self, request, response, spider):
>   >  # 修改响应内容,避免乱码
>   >  response = HtmlResponse(url=response.url, body=response.body, encoding='utf-8')
>   >  return response
>   > ```
>   
> - 使用selenium加载动态内容
>
>   > 1. 创建 模拟浏览器对象spider.brower
>   >
>   >    > ```python
>   >    > from selenium import webdriver
>   >    > 
>   >    > firefox_options = webdriver.FirefoxOptions()
>   >    > firefox_options.add_argument("--headless")
>   >    > firefox_options.add_argument("--disable-gpu")
>   >    > 
>   >    > 
>   >    > class ScrapyseleniumPipeline:
>   >    >  # spider 就是自定义爬虫类的实例化对象
>   >    >     def open_spider(self, spider):
>   >    >         spider.brower = webdriver.Firefox(executable_path="./geckodriver.exe", options=firefox_options)
>   >    > 
>   >    >     def close_spider(self, spider):
>   >    >         spider.brower.close()
>   >    > 
>   >    >     def process_item(self, item, spider):
>   >    >         return item
>   >    > ```
>   >
>   > 2. 下载中间件利用spider传递过来的浏览器对象发送请求，构造新的响应对象
>   >
>   >    > ```python
>   >    > def process_response(self, request, response, spider):
>   >    >     brower = spider.brower
>   >    >     brower.get(request.url)
>   >    >     resp = brower.page_source
>   >    > 
>   >    >     return HtmlResponse(url=request.url, body=resp, encoding="utf-8", request=request)
>   >    > ```



---



### 备注

#### selenium

##### 基本使用

> - 模拟浏览器发送请求
>
>   > ```python
>   > from selenium import webdriver
>   > 
>   > # executable_path浏览器驱动位置
>   > # 创建浏览器对象
>   > brower = webdriver.Firefox(executable_path="./geckodriver.exe")
>   > # 模拟浏览器发送请求
>   > brower.get("http://www.baidu.com")
>   > # 获取网页源代码
>   > brower.page_source
>   > # 关闭浏览器对象,释放资源
>   > borwer.close()
>   > ```
>
> - 元素定位
>
>   > ```python
>   > from selenium.webdriver.common.by import By
>   > from selenium import webdriver
>   > 
>   > brower = webdriver.Firefox(executable_path="./geckodriver.exe")
>   > # 通过元素id定位
>   > e1=brower.find_element(By.ID, "元素id")
>   > # e1执行点击事件
>   > e1.click()
>   > # 通过xpath表达式定位元素
>   > e2=brower.find_element(By.XPATH,"xpath表达式")
>   > # 给输入框填写文本
>   > e.send_keys("文本内容")
>   > 
>   > # 查找元素的方式有如下几种:
>   > """
>   > Set of supported locator strategies.
>   > 
>   > find_element(By.ID | By.NAME |By.CLASS_NAME ......
>   > 
>   > ID = "id"
>   > XPATH = "xpath"
>   > LINK_TEXT = "link text"
>   > PARTIAL_LINK_TEXT = "partial link text"
>   > NAME = "name"
>   > TAG_NAME = "tag name"
>   > CLASS_NAME = "class name"
>   > CSS_SELECTOR = "css selector"
>   > """
>   > ```



---



##### 无头模式

> ```python
> from selenium import webdriver
> 
> firefox_options = webdriver.FirefoxOptions()
> firefox_options.add_argument("--headless")
> firefox_options.add_argument("--disable-gpu")
> 
> webdriver.Firefox(executable_path="./geckodriver.exe", options=firefox_options)
> ```



##### 等待方式

> - 强制等待
>
>   > ```python
>   > # 设置多少秒页面就会等待多少秒,这个方法很容易让线程挂掉，使程序抛异常，所以要慎用此方法
>   > import time
>   > time.sleep(3)
>   > ```
>
> - 隐式等待
>
>   > ```python
>   > """
>   > 一次设置，全局生效。不要当作固定等待使用，不要每次需要等待时都写一次隐式等待
>   > 隐式等待设置了一个最长等待时间，在规定时间内网页加载完成(也就是一般情况下你看到浏览器标签栏那个小圈不再转就代表加载完成)，则执行下一步，否则一直等到时间结束，然后执行下一步
>   > """
>   > from selenium import webdriver
>   > from selenium.webdriver.common.by import By
>   > 
>   > brower = webdriver.Firefox(executable_path="./geckodriver.exe")
>   > 
>   > # 开启隐式等待  全局设置
>   > brower.implicitly_wait(10)
>   > 
>   > brower.get("https://www.github.com")
>   > brower.find_element(By.XPATH,"//div")
>   > ```
>
> - 显示等待
>
>   > ```python
>   > """
>   > 当等待的条件满足后（一般用来判断需要等待的元素是否加载出来），就继续下一步操作。等不到就一直等，如果在规定的时间之内都没找到，那么就跳出Exception。
>   > 
>   > 使用显示等待前需先导入显示等待所需模块和等待条件
>   > """
>   > 
>   > 
>   > from selenium import webdriver
>   > from selenium.webdriver.common.by import By
>   > 
>   > # 显式等待模块
>   > from selenium.webdriver.support.ui import WebDriverWait
>   > # 显式等待条件
>   > from selenium.webdriver.support import expected_conditions as ec
>   > 
>   > brower = webdriver.Firefox(executable_path="./geckodriver.exe")
>   > 
>   > 
>   > """
>   > 元素是否存在判断条件如下:
>   > title_is(str)  str是期望匹配标题
>   > title_contains(str)  str是期望元素包含某些内容
>   > presence_of_element_located  判断期待元素是否存在
>   > ...... url_matches
>   > """
>   > 
>   > """
>   > 10是最长等待时间，0.5是每0.5秒去查询对应的元素
>   > until后面跟的等待具体条件，EC是判断条件，检查元素是否存在于页面的 DOM上
>   > """
>   > login_btn = WebDriverWait(brower, 10, 0.5).until(ec.presence_of_element_located((By.ID, "s-top-loginbtn")))
>   > 
>   > if login_btn is not None:
>   >     login_btn.click()
>   > ```
>   >