---
title: Scrapy study
date: 2021-06-29 16:21:55
tags: ['Python','Scrapy','Crawler']
categories: 编程 Programming
---
# 简单的实例

## 开始项目
### 创建项目
- Syntax: **scrapy startproject <project_name> [project_dir]**

### 添加新spider
- Syntax: **scrapy genspider [-t template] <name> <domain>**
<!-- more -->
## 实例1
这是一个简单的Spider类(继承于官方的class),其拥有一个唯一的name属性(不能和其他组建同名),start_requests生成器含有最开始爬取的urls并对每个url使用Request以获取相对应的response
{% codeblock lang:Python %}
import scrapy

class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)
#根据初始的url用Request函数返回每个url对应的response
#这个method必须是生成器
{% endcodeblock %}

parse method用于解析所获得的response,这里将所获得html信息储存到本地文件
{% codeblock lang:Python %}
#class QuotesSpider(scrapy.Spider):
#...
    def parse(self, response):
        page = response.url.split("/")[-2]
        filename = f'quotes-{page}.html'
        with open(filename, 'wb') as f:
            f.write(response.body)
        self.log(f'Saved file {filename}')
{% endcodeblock %}

根据Scrapy内置的功能可以进一步简化代码的结构,这里可以忽略start_requests生成器并只使用一个start_urls列表
同时这段代码精进了解析的部分,使用css selector来选择html页面的特定信息,并储存在一个字典中
因为这是一个生成器,当前页面爬取完毕之后会处理下一个yield指令--获取下一个页面的url信息并执行Request得到response
{% codeblock lang:Python %}
class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'http://quotes.toscrape.com/page/1/',
    ]
#scrapy内置有method直接可以读取start_urls

    def parse(self, response):
        for quote in response.css('div.quote'):
            #quote 依旧是selector类型的object
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
                #get method 获取根据css得到的信息 
                #Serialize and return the matched nodes in a single unicode string. 
                #Percent encoded content is unquoted.
                'tags': quote.css('div.tags a.tag::text').getall(),
                #.getall() returns a list with all results
            }

        next_page = response.css('li.next a::attr(href)').get()
        if next_page is not None:
            next_page = response.urljoin(next_page)
            #We use the urljoin() since the links can be relative
            yield scrapy.Request(next_page, callback=self.parse)
{% endcodeblock %}

最后部分的urljoin()可以优化使用response.follow method,其更加便捷(处理相对路径),但仍需要yield response
{% codeblock lang:Python %}
#class QuotesSpider(scrapy.Spider):
#...
    #def parse(self, response):
    #...
        if next_page is not None:
            yield response.follow(next_page, callback=self.parse)
{% endcodeblock %}

这里也可以直接传递一个selector对象到response.follow     
{% codeblock lang:Python %}
#class QuotesSpider(scrapy.Spider):
#...
    #def parse(self, response):
    #...
        for href in response.css('ul.pager a::attr(href)'):
        #对于<a> element 可以直接写成response.css('ul.pager a')
            yield response.follow(href, callback=self.parse)
{% endcodeblock %}
当有个requests的iterable可以直接使用follow_all method
{% codeblock lang:Python %}
#class QuotesSpider(scrapy.Spider):
#...
    #def parse(self, response):
    #...
    yield from response.follow_all(css='ul.pager a', callback=self.parse)
{% endcodeblock %}
 By default, Scrapy filters out duplicated requests to URLs already visited, avoiding the problem of hitting servers too much because of a programming mistake. This can be configured by the setting **DUPEFILTER_CLASS**

 ## 实例2
{% codeblock lang:Python %}
 import scrapy


class AuthorSpider(scrapy.Spider):
    name = 'author'

    start_urls = ['http://quotes.toscrape.com/']

    def parse(self, response):
        author_page_links = response.css('.author + a')
        yield from response.follow_all(author_page_links, self.parse_author)

        #当前页面的author信息爬取完毕后获取下个下个页面的url并爬取
        pagination_links = response.css('li.next a')
        yield from response.follow_all(pagination_links, self.parse)

    def parse_author(self, response):
        def extract_with_css(query):
            return response.css(query).get(default='').strip()

        yield {
            'name': extract_with_css('h3.author-title::text'),
            'birthdate': extract_with_css('.author-born-date::text'),
            'bio': extract_with_css('.author-description::text'),
        }
{% endcodeblock %}

## 命令行操作

### 存储爬取到的信息
在命令行中输入以下代码
{% codeblock %}
scrapy crawl quotes -O quotes.json
{% endcodeblock %}
其中 
- **quotes** 代表crawler的名字
- **-O** switch overwrites any existing file
- **-o** append new content to any existing file
- 可以将最后文件格式换成其他格式如.jl

### 添加属性
在命令行中输入以下代码
{% codeblock %}
scrapy crawl quotes -O quotes-humor.json -a tag=humor
{% endcodeblock %}
**-a** command 将humor添加为spider的属性这样就可以使用tag属性来访问特定的网页
