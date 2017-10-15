<div class="page-wrapper" tabindex="-1" role="main">

<div class="page-inner">

<section class="normal" id="section-">

# Scrapy 框架

*   Scrapy是用纯Python实现一个为了爬取网站数据、提取结构性数据而编写的应用框架，用途非常广泛。

*   框架的力量，用户只需要定制开发几个模块就可以轻松的实现一个爬虫，用来抓取网页内容以及各种图片，非常之方便。

*   Scrapy 使用了 Twisted`['twɪstɪd]`(其主要对手是Tornado)异步网络框架来处理网络通讯，可以加快我们的下载速度，不用自己去实现异步框架，并且包含了各种中间件接口，可以灵活的完成各种需求。

## Scrapy架构图(绿线是数据流向)：

![](../images/scrapy_all.png)

*   `Scrapy Engine(引擎)`: 负责`Spider`、`ItemPipeline`、`Downloader`、`Scheduler`中间的通讯，信号、数据传递等。

*   `Scheduler(调度器)`: 它负责接受`引擎`发送过来的Request请求，并按照一定的方式进行整理排列，入队，当`引擎`需要时，交还给`引擎`。

*   `Downloader（下载器）`：负责下载`Scrapy Engine(引擎)`发送的所有Requests请求，并将其获取到的Responses交还给`Scrapy Engine(引擎)`，由`引擎`交给`Spider`来处理，

*   `Spider（爬虫）`：它负责处理所有Responses,从中分析提取数据，获取Item字段需要的数据，并将需要跟进的URL提交给`引擎`，再次进入`Scheduler(调度器)`，

*   `Item Pipeline(管道)`：它负责处理`Spider`中获取到的Item，并进行进行后期处理（详细分析、过滤、存储等）的地方.

*   `Downloader Middlewares（下载中间件）`：你可以当作是一个可以自定义扩展下载功能的组件。

*   `Spider Middlewares（Spider中间件）`：你可以理解为是一个可以自定扩展和操作`引擎`和`Spider`中间`通信`的功能组件（比如进入`Spider`的Responses;和从`Spider`出去的Requests）

**只有当`调度器`中不存在任何request了，整个程序才会停止，（也就是说，对于下载失败的URL，Scrapy也会重新下载。）**

### 制作 Scrapy 爬虫 一共需要4步：

*   新建项目  (scrapy startproject xxx)：新建一个新的爬虫项目
*   抓取的目标 （编写items.py）：明确你想要抓取的目标
*   制作爬虫 （spiders/xxspider.py）：制作爬虫开始爬取网页
*   存储内容 （pipelines.py）：设计管道存储爬取内容

</section>

</div>

</div>
