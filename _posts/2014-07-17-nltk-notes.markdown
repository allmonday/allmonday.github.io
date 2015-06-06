---
layout: post
title:  "NLTK: python natural language toolkit"
categories: jekyll update
tags: 未完有机会待续
---

##corpus 语料处理

###nltk's book
基本操作

```python
from nltk.book import *
text1.concordance("monstrous")  #查看monstrous的上下文
text1.similar("monstrous") #查看上下文相同的词
text1.common_contexts(["monstrous", "very"])  #相同的上下文
text1.generate() #输出随机段落
```

词频统计

```python
#freqdist 统计词频
from nltk import *
from nltk.book import *
fdist1 = freqdist(text1)
fdist1['whale']  #whale这个词出现的次数
fdist1.plot(50, cumulative=true) #统计图,前50个词的累积图
#!!高频词太常见,低频词太多

#长度大于7并且频次大于7
fdist5 = FreqDist(text5)
sorted([w for w in set(text5) if len(w) > 7 and fdist5[w] > 7)
```

搭配和双连词

```python
#tool
bigrams(["more", "is", "said", "than"])
text4.collocations()  #输出text4的高频双连词
```

###NLTK频率分布类中定义的函数

```
fdist = FreqDist(samples) 
fdist.inc(samples)
fdist['monstrous']
fdist.freq('monstrous')
fdist.N()
fdist.keys()
for sample in fdist:  #descending
fdist.max()
fdist.tabulate()
fdist.plot()
fdist.plot(cumulative=True)
fdist1 < fdist2 
```

###处理网络信息

```python
from urllib2 import urlopen
url = "http://news.bbc.co.uk/2/hi/health/2284783.stm"
html = urlopen(url).read()
raw = nltk.clean_html(html)
tokens = nltk.word_tokenize(raw)
text = nltk.Text(tokens)
text.concordance('gene')
```

###work flow

```python
#html->ascii->text->vocab
html = urlopen(url).read()
raw = nltk.clean_html(html)
raw = raw[xx : xxx]

tokens = nltk.word_tokenize(raw)
tokens = tokens[xx: xxx]
text = nltk.Text(tokens)

words = [w.lower() for w in text]
vocab = sorted(set(words))
```


###对中文进行分析

```python
words = open('xxx.data', 'r').read().decode('utf-8').split(' ')
fdist = FreqDist(words)

from nltk.corpus import PlaintextCorpusReader
corpus_root = './dict'
wordlists = PlaintextCorpusReader(corpus_root, ".*")
wordlists.fileids()
wordlist.words()
```

nltk.Text()

```python
unicode_text = u'\u015bwiatowej'
unicode_text = unicode_text.encode("utf-8")
tokens = nltk.word_tokenize(unicode_text)
text = nltk.Text(tokens)
```

###wordnet

```python
from nltk.corpus import wordnet as wn
wn.synsets('motorcar')
wn.synset('car.n.01').lemma_names #同义词集
wn.synset('car.n.01').definition #定义
wn.synset('car.n.01').examples
```

###unicode

```python
import codecs
f = codecs.open(path, encoding="utf-8")
```
