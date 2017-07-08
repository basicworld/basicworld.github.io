---
layout: single
title: 优秀的IP池开源项目IPProxyPool
date: 2017-06-07
category: tech
tags: tech
description: 使用python开发的优秀的IP池开源项目IPProxyPool
---

<!-- add content here -->
爬虫一定会涉及到IP反爬问题，解决方式有：

1. 设置延迟，爬慢一点---不能接受
2. 使用IP池--购买ip池太贵，自建ip池是个好选择
3. 使用第三方平台，如crawlera--亲测如果不付费的话，速度非常慢，不能接受

自建ip池也是挺麻烦的，涉及：网址维护、爬虫、解析、更新策略、api接口等等

所以，隆重推荐开源的ip池项目
[IPProxyPool](https://github.com/qiyeboy/IPProxyPool)。项目已经把自建ip池的所有工作都给你做好了，需要做的就是：在服务器上把这个服务启动就可以，github上游完整的说明

scrapy搭配ip池，简直不要太棒

scrapy的middlewares.py添加以下配置

```python

from scrapy import signals
import random
import scrapy
from scrapy import log
import requests
import json


class RandomUserAgent(object):
    """Randomly rotate user agents based on a list of predefined ones"""

    def __init__(self, agents):
        self.agents = agents

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler.settings.getlist('USER_AGENTS'))

    def process_request(self, request, spider):
    #print "**************************" + random.choice(self.agents)
        request.headers.setdefault('User-Agent', random.choice(self.agents))

# logger = logging.getLogger()

class ProxyMiddleWare(object):
    """docstring for ProxyMiddleWare"""
    def __init__(self):
        self.ip_proxies_request_count = 100

    def process_request(self,request, spider):
        '''对request对象加上proxy'''
        proxy = self.get_random_proxy()
        print("get ip:"+proxy)
        request.meta['proxy'] = proxy

    def process_response(self, request, response, spider):
        '''对返回的response处理'''
        # 如果返回的response状态不是200，重新生成当前request对象
        if response.status != 200:
            proxy = self.get_random_proxy()
            print("error request, change ip to:"+proxy)
            # 对当前reque加上代理
            request.meta['proxy'] = proxy
            return request
        return response

    def get_random_proxy(self):
        '''随机从文件中读取proxy'''
        ip_ports = []
        if not ip_ports or self.ip_proxies_request_count < 0:
            r = requests.get('http://your_ip:8000/?types=0&count=100&country=国内')
            ip_ports = json.loads(r.text)
            self.ip_proxies_request_count = 100
        self.ip_proxies_request_count -= 1
        proxies = ['http://%s:%s' % (ip_port[0], ip_port[1]) for ip_port in ip_ports]
        proxy = random.choice(proxies)
        return proxy

```

settings.py添加一下配置：
```python

DOWNLOADER_MIDDLEWARES = {
#    'cnblogs.middlewares.MyCustomDownloaderMiddleware': 543,
    'your_project.middlewares.RandomUserAgent': 1,  # 随机user agent
    #'scrapy.contrib.downloadermiddleware.httpproxy.HttpProxyMiddleware': 110, #此API已经弃用
    #'scrapy.downloadermiddlewares.httpproxy.HttpProxyMiddleware': 110, #代理需要用到
    'your_project.middlewares.ProxyMiddleWare': 100,  # 代理需要用到
}

# Enable or disable extensions
# See http://scrapy.readthedocs.org/en/latest/topics/extensions.html
#EXTENSIONS = {
#    'scrapy.extensions.telnet.TelnetConsole': None,
#}

# Configure item pipelines
# See http://scrapy.readthedocs.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
   'your_project.pipelines.your_projectPipeline': 300,
   'your_project.pipelines.JsonWithEncodingCnblogsPipeline': 400,
}

USER_AGENTS = [
    "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; AcooBrowser; .NET CLR 1.1.4322; .NET CLR 2.0.50727)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0; Acoo Browser; SLCC1; .NET CLR 2.0.50727; Media Center PC 5.0; .NET CLR 3.0.04506)",
    "Mozilla/4.0 (compatible; MSIE 7.0; AOL 9.5; AOLBuild 4337.35; Windows NT 5.1; .NET CLR 1.1.4322; .NET CLR 2.0.50727)",
    "Mozilla/5.0 (Windows; U; MSIE 9.0; Windows NT 9.0; en-US)",
    "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 2.0.50727; Media Center PC 6.0)",
    "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 1.0.3705; .NET CLR 1.1.4322)",
    "Mozilla/4.0 (compatible; MSIE 7.0b; Windows NT 5.2; .NET CLR 1.1.4322; .NET CLR 2.0.50727; InfoPath.2; .NET CLR 3.0.04506.30)",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN) AppleWebKit/523.15 (KHTML, like Gecko, Safari/419.3) Arora/0.3 (Change: 287 c9dfb30)",
    "Mozilla/5.0 (X11; U; Linux; en-US) AppleWebKit/527+ (KHTML, like Gecko, Safari/419.3) Arora/0.6",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.2pre) Gecko/20070215 K-Ninja/2.1.1",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN; rv:1.9) Gecko/20080705 Firefox/3.0 Kapiko/3.0",
    "Mozilla/5.0 (X11; Linux i686; U;) Gecko/20070322 Kazehakase/0.4.5",
    "Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.8) Gecko Fedora/1.9.0.8-1.fc10 Kazehakase/0.5.6",
    "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.56 Safari/535.11",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_3) AppleWebKit/535.20 (KHTML, like Gecko) Chrome/19.0.1036.7 Safari/535.20",
    "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; fr) Presto/2.9.168 Version/11.52",
]
```
