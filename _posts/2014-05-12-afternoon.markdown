---
layout: post
title:  "不知道做什么的时候可以做的事"
categories: jekyll update
tags: 坚持住你可以的
---
##math
线性代数

统计学

## python 
python algorithm

pypi

python webapp 框架 eg:`flask, django, tornado, web.py`

buildout `还是npm爽啊`

搭环境 `python3 venv` `virtualenv` `pyenv`

python源码分析

jieba分词源码 `trie tree` `隐马尔科夫`

numpy 数学

skilearn, kmeans, knn and so on..

scipy

## web
js爬虫 `node` `casperjs` `phantomjs`

响应式web设计,bootstrap,emmet

nodejs,框架 `express` `koa`

http: the definitive guide `看中文版吧先` `编码那章很重要`

d3库

canvas动画 `pixi`

css

angularjs `啊哈哈`

html

javascript

##data
pyquery `有jquery要毛pyquery`

regular expression

vim配置

python for data analysis; pandas, ipython, numpy

##
如何用raspberry pi 搭配arduino

shell commands

##display

matplotlib

latex, write math formulas


##language

how to made a language

little scheme

##books to read

The Design of Everyday Things  from wang yin

A Little Java, A Few Patterns  from wang yin


##argparse 

[link to argparse] (https://docs.python.org/dev/library/argparse.html)

```python
import argparse
parser = argparse.ArgumentParser(description="feature extractor")
parser.add_argument('-f', '--file-path', dest='fp',
                    help="extract from text file")
parser.add_argument('-t', '--text', dest='text',
					help="extract from text")
args = parser.parse_args()

if args.fp:
    print("welcome from file path")
elif args.text:
    print("welcome from text")
```

##rq  
[link to rq] (http://python-rq.org/)

###my_module.py  
```python
import requests

def count_words_at_url(url):
    resp = requests.get(url)
        return len(resp.text.split())
```

###rq_queue.py
```python
from redis import Redis
from rq import Queue
from my_module import count_words_at_url

q = Queue(connection=Redis())

result = q.enqueue(
     count_words_at_url, 'http://www.baidu.com'
)
```

###i_queue.py
```python
from rq import Connection, Queue
from redis import Redis
from worker import count_words_at_url
import time

redis_conn = Redis(password="caigen100")
q = Queue(connection=redis_conn)

job = q.enqueue(count_words_at_url, "http://www.baidu.com")
print(job.result)

time.sleep(2)
print(job.result)
```

if you open rqworker, you will see the job running..

##redis 
```python
import redis  
r = redis.Redis(host="localhost", port=6379, db=0, password="xxx")  
r.set('foo', 'bar')  
print(r.get('foo'))  
r.delete('foo')  
r.dbsize()  
r.save()  
r.flushdb()  
r.keys()  
```

##sphinx  
##logging  
##unittest  
##re  
##git
##encoding!!!!!

##git branch co!!


