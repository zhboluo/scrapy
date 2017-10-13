<div class="page-wrapper" tabindex="-1" role="main">

<div class="page-inner">

<section class="normal" id="section-">

# Spider

Spider类定义了如何爬取某个(或某些)网站。包括了爬取的动作(例如:是否跟进链接)以及如何从网页的内容中提取结构化数据(爬取item)。 换句话说，Spider就是您定义爬取的动作及分析某个网页(或者是有些网页)的地方。

`class scrapy.Spider`是最基本的类，所有编写的爬虫必须继承这个类。

主要用到的函数及调用顺序为：

`__init__()` : 初始化爬虫名字和start_urls列表

`start_requests() 调用make_requests_from url()`:生成Requests对象交给Scrapy下载并返回response

`parse()` : 解析response，并返回Item或Requests（需指定回调函数）。Item传给Item pipline持久化 ， 而Requests交由Scrapy下载，并由指定的回调函数处理（默认parse())，一直进行循环，直到处理完所有的数据为止。

#### 源码参考

```
#所有爬虫的基类，用户定义的爬虫必须从这个类继承
class Spider(object_ref):

    #定义spider名字的字符串(string)。spider的名字定义了Scrapy如何定位(并初始化)spider，所以其必须是唯一的。
    #name是spider最重要的属性，而且是必须的。
    #一般做法是以该网站(domain)(加或不加 后缀 )来命名spider。 例如，如果spider爬取 mywebsite.com ，该spider通常会被命名为 mywebsite
    name = None

    #初始化，提取爬虫名字，start_ruls
    def __init__(self, name=None, **kwargs):
        if name is not None:
            self.name = name
        # 如果爬虫没有名字，中断后续操作则报错
        elif not getattr(self, 'name', None):
            raise ValueError("%s must have a name" % type(self).__name__)

        # python 对象或类型通过内置成员__dict__来存储成员信息
        self.__dict__.update(kwargs)

        #URL列表。当没有指定的URL时，spider将从该列表中开始进行爬取。 因此，第一个被获取到的页面的URL将是该列表之一。 后续的URL将会从获取到的数据中提取。
        if not hasattr(self, 'start_urls'):
            self.start_urls = []

    # 打印Scrapy执行后的log信息
    def log(self, message, level=log.DEBUG, **kw):
        log.msg(message, spider=self, level=level, **kw)

    # 判断对象object的属性是否存在，不存在做断言处理
    def set_crawler(self, crawler):
        assert not hasattr(self, '_crawler'), "Spider already bounded to %s" % crawler
        self._crawler = crawler

    @property
    def crawler(self):
        assert hasattr(self, '_crawler'), "Spider not bounded to any crawler"
        return self._crawler

    @property
    def settings(self):
        return self.crawler.settings

    #该方法将读取start_urls内的地址，并为每一个地址生成一个Request对象，交给Scrapy下载并返回Response
    #该方法仅调用一次
    def start_requests(self):
        for url in self.start_urls:
            yield self.make_requests_from_url(url)

    #start_requests()中调用，实际生成Request的函数。
    #Request对象默认的回调函数为parse()，提交的方式为get
    def make_requests_from_url(self, url):
        return Request(url, dont_filter=True)

    #默认的Request对象回调函数，处理返回的response。
    #生成Item或者Request对象。用户必须实现这个类
    def parse(self, response):
        raise NotImplementedError

    @classmethod
    def handles_request(cls, request):
        return url_is_from_spider(request.url, cls)

    def __str__(self):
        return "<%s %r at 0x%0x>" % (type(self).__name__, self.name, id(self))

    __repr__ = __str__

```

#### 主要属性和方法

*   name

    > 定义spider名字的字符串。
    > 
    > 例如，如果spider爬取 mywebsite.com ，该spider通常会被命名为 mywebsite

*   allowed_domains

    > 包含了spider允许爬取的域名(domain)的列表，可选。

*   start_urls

    > 初始URL元祖/列表。当没有制定特定的URL时，spider将从该列表中开始进行爬取。

*   start_requests(self)

    > 该方法必须返回一个可迭代对象(iterable)。该对象包含了spider用于爬取（默认实现是使用 start_urls 的url）的第一个Request。
    > 
    > 当spider启动爬取并且未指定start_urls时，该方法被调用。

*   parse(self, response)

    > 当请求url返回网页没有指定回调函数时，默认的Request对象回调函数。用来处理网页返回的response，以及生成Item或者Request对象。

*   log(self, message[, level, component])

    > 使用 scrapy.log.msg() 方法记录(log)message。 更多数据请参见 [logging](4.7.html)

#### 案例：腾讯招聘网自动翻页采集

*   创建一个新的爬虫：

`scrapy genspider tencent "tencent.com"`

*   编写items.py

获取职位名称、详细信息、

```
class TencentItem(scrapy.Item):
    name = scrapy.Field()
    detailLink = scrapy.Field()
    positionInfo = scrapy.Field()
    peopleNumber = scrapy.Field()
    workLocation = scrapy.Field()
    publishTime = scrapy.Field()

```

*   编写tencent.py

```
# tencent.py

from mySpider.items import TencentItem
import scrapy
import re

class TencentSpider(scrapy.Spider):
    name = "tencent"
    allowed_domains = ["hr.tencent.com"]
    start_urls = [
        "http://hr.tencent.com/position.php?&start=0#a"
    ]

    def parse(self, response):
        for each in response.xpath('//*[@class="even"]'):

            item = TencentItem()
            name = each.xpath('./td[1]/a/text()').extract()[0]
            detailLink = each.xpath('./td[1]/a/@href').extract()[0]
            positionInfo = each.xpath('./td[2]/text()').extract()[0]
            peopleNumber = each.xpath('./td[3]/text()').extract()[0]
            workLocation = each.xpath('./td[4]/text()').extract()[0]
            publishTime = each.xpath('./td[5]/text()').extract()[0]

            #print name, detailLink, catalog, peopleNumber, workLocation,publishTime

            item['name'] = name.encode('utf-8')
            item['detailLink'] = detailLink.encode('utf-8')
            item['positionInfo'] = positionInfo.encode('utf-8')
            item['peopleNumber'] = peopleNumber.encode('utf-8')
            item['workLocation'] = workLocation.encode('utf-8')
            item['publishTime'] = publishTime.encode('utf-8')

            curpage = re.search('(\d+)',response.url).group(1)
            page = int(curpage) + 10
            url = re.sub('\d+', str(page), response.url)

            # 发送新的url请求加入待爬队列，并调用回调函数 self.parse
            yield scrapy.Request(url, callback = self.parse)

            # 将获取的数据交给pipeline
            yield item

```

*   编写pipeline.py文件

```
import json

#class JsonPipeline(object):
class TencentJsonPipeline(object):

    def __init__(self):
        #self.file = open('teacher.json', 'wb')
        self.file = open('tencent.json', 'wb')

    def process_item(self, item, spider):
        content = json.dumps(dict(item), ensure_ascii=False) + "\n"
        self.file.write(content)
        return item

    def close_spider(self, spider):
        self.file.close()

```

*   在 setting.py 里设置ITEM_PIPELINES

```
ITEM_PIPELINES = {
    #'mySpider.pipelines.SomePipeline': 300,
    #"mySpider.pipelines.JsonPipeline":300
    "mySpider.pipelines.TencentJsonPipeline":300
}

```

*   执行爬虫：`scrapy crawl tencent`

## 思考

#### 请思考 parse()方法的工作机制：

```

1. 因为使用的yield，而不是return。parse函数将会被当做一个生成器使用。scrapy会逐一获取parse方法中生成的结果，并判断该结果是一个什么样的类型；
2. 如果是request则加入爬取队列，如果是item类型则使用pipeline处理，其他类型则返回错误信息。
3. scrapy取到第一部分的request不会立马就去发送这个request，只是把这个request放到队列里，然后接着从生成器里获取；
4. 取尽第一部分的request，然后再获取第二部分的item，取到item了，就会放到对应的pipeline里处理；
5. parse()方法作为回调函数(callback)赋值给了Request，指定parse()方法来处理这些请求 scrapy.Request(url, callback=self.parse)
6. Request对象经过调度，执行生成 scrapy.http.response()的响应对象，并送回给parse()方法，直到调度器中没有Request（递归的思路）
7. 取尽之后，parse()工作结束，引擎再根据队列和pipelines中的内容去执行相应的操作；
8. 程序在取得各个页面的items前，会先处理完之前所有的request队列里的请求，然后再提取items。
7. 这一切的一切，Scrapy引擎和调度器将负责到底。

```



</section>

</div>

</div>
