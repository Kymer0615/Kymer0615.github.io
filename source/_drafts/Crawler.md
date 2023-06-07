---
title: Crawler study
date: 2021-07-21 16:22:55
tags: ['Python','Scrapy','Crawler']
categories: 编程 Programming
---
# Crawler理解
- 找到主网页下的URL列表
    1. 直接通过**Xpath解析**获得所需要的URL
        - 找到@href属性下的URL字段可能并不完整,需要后续通过观察拼接来得到最终字段
    2. 主网页无法直接获取的情况可以通过**观察XHR来判断数据获取的方式**,一般使用GET或者POST来获取一个JSON文件
        - 找到JSON对应的key就可以得到URL
    3. 主网页使用JavaScript生成动态页面的情况可以使用**selenium来模拟浏览器访问**或者**观察a 标签所包含的值与对应页面的关系,然后进行字段的拼接**
- 获得访问下一页的URL
    1. 解决方案和上面类似,在拼接字段的时候可能使用**正则**来处理对应情况
    2. 需要设计如何控制访问页数,可能需要通过正则从URL获取相关信息或者从JSON里获取[或者直接控制总的量]
- 对具体页面进行解析
    1. 使Xpath获取对应的信息
    2. 使用正则来处理获取之后的信息
    3. 下载链接或者图片的链接很可能并非完整
    4. 
- 设计
    1. 使用stream的思想叠加method来解析
    2. 控制访问的速率特别是异步的情况
    3. 使用IP池
    
# Issues
- 国标网站抓取
    - 对于国标网站进行抓取时,Requests库中的session无法自动传递Cookie
        - 使用r.request.prepare_cookies(r.cookies)来传递
    - 下载附件时需要验证码
        - 使用CNN来训练模型,训练正确率可达99%,满足实际使用


