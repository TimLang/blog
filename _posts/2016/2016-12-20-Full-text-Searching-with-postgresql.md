---
layout: post
title: 使用posgresql作为全文检索服务
categories:
- 数据库
- posgresql
- pg
- 全文检索
tags:
- 数据库
- posgresql
- pg
- 全文检索

---


###为什么用Postgresql提供全文检索服务
如果想给自己的网站提供搜索服务的话有很多选择，比如：Solr, ElasticSearch等。但是当我在本地装好elasticsearch准备进行调试的时候发现实在太重量级了，关键问题是elasticsearch的启动耗费了2G多的内存，查了下发现elasticsearch也是基于lucence的，难怪这么耗内存，java的application都这样。于是我就寻找轻量级的解决方案，当然是基于内存考虑的，于是发现了postgresql的全文检索，最关键的是已经有国人做好了中文分词了。

###Postgresql中文分词的安装

#### 下载

##### 首先要安装SCWS:

wget -q -O - http://www.xunsearch.com/scws/down/scws-1.2.2.tar.bz2 | tar xf -

下载完后进目录进行安装，上面的脚本已经帮你完成解压操作了。

```shell

	cd scws-1.2.2	
  	./configure  
  	make install
  
```

##### 安装zhparser：
zhparser是一个postgresql的插件，安装也很简单：

```shell

	git clone https://github.com/amutu/zhparser.git
	SCWS_HOME=/usr/local make && make install
	
```

##### 配置你的postgresql数据库：

```shell

	psql -U yourusername
	
	CREATE EXTENSION zhparser; #生成zhparser插件
	
	CREATE TEXT SEARCH CONFIGURATION YOURNAMESPACE (PARSER = zhparser); #生产配置文件，其实也就是初始化
	
	create index idx_gin_YOURTABLENAME_name on YOURTABLENAME using gin(to_tsvector('YOURNAMESPACE', title)); #为某张数据表创建索引。
	
	select TITLE from YOURTABLENAME where to_tsvector('YOURNAMESPACE',title) @@ to_tsquery('YOURNAMESPACE', '全文检索'); #这里就能看到结果了，性能还不错，肯定比like的靠谱多了，当然主要取决你的中文分词，英文则无次问题。
    
```

