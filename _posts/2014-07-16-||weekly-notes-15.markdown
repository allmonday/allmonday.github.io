---
layout: post
title:  "工作记录 - nlp, etl, matplotlib"
categories: jekyll update
---

##nlp
中文分词词性笔记－以ICTCLAS.doc为参考  --只记录词量比较多的

###名词 n
abbr | description
------| -------
`nr` |人名
`ns` |地名 [jieba中普遍不准确]
`nt` |机构团体名
`nz` |专有名
`ng` |名词性语素 [eg:　仆,仙,俑]
`nbr`|品牌名-自定义

###时间 t
abbr | description
------| -------
`t`|时间　[eg:　半世纪]

###动词 v
abbr | description
------| -------
`vd`|副动词
`vn`|名动词 [jieba中较多]
`vshi`|是
`vyou`|有
`vi`|不及物动词
`vg`|动词性语素 [eg: 怂,怒]

###形容词 a
abbr | description
------| -------
`a`|形容词 [eg: 很大]
`ad`|副词形容词 [eg: 慌忙,显然]
`an`|名词性形容词 [eg: 温暖,狼狈]
`ag`|形容词语素 [eg: 私,裸]

###状态词 z
abbr | description
------| -------
`z`|[eg:　怪怪的,　甜甜的]

###代词 r
abbr | description
------| -------
`r`|代词
`rz`|指示代词
`rr`|人称代词　[eg: 其它人]

###数词 m
abbr | description
------| -------
`m`|数词 [eg: 四分之三]
`mq`|数量词 [eg: 这回,这点,一度,一月份,这件] 

###量词 q
abbr | description
------| -------
`q`|量词 [eg: 段,盒,箱]

###副词 d
abbr | description
------| -------
`d`|副词 [eg: 多于,很,不] <= `不`需要单独强调

###介词 p
abbr | description
------| -------
`p`|[eg: 为了,于,为]
`pba`|把
`pbei`|被
`pwei`|为-自定义

###连词 c
abbr | description
------| -------
`c`|[eg: 接过,故此,故而]

###助词 u
abbr | description
------| -------
`ul`|了
`uz`|着
`ug`|过
`ud`|得
`uj`|的
`uv`|地

###叹词 e
abbr | description
------| -------
`e`|eg: 哇

###拟声词 o
abbr | description
------| -------
`o`|哈哈

###语气词 y
abbr | description
------| -------
`y`|[eg: 唔,哩,啦]

##nlp-sites

```
www.getsoshio.com
www.lexalytics.com
```

##etl
###issue-price
use barplot's concept, calculate the Q1, Q3 and IQR, than find
values out of range.


##matplotlib

```python
import matplotlib.pyplot as plt

data = [1,1,2,3,4,4,4,3,5,4,1]
common_params = dict(
    bins=50,
    normed=True,
    facecolor="gray"
)
plt.hist(data, **common_params)
plt.title("histogram")
plt.xlabel("price")
plt.ylabel("Frequency")
plt.show()

```

