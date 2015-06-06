---
layout: post
title:  "python 标准库"
date:   2014-05-27 08:24:28
categories: jekyll update
---


##collection.defalutdict
defalut data type

```python
from collections import defaultdict
s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
d = defaultdict(list)
for k, v in s:
        d[k].append(v)
        print d.items()
    
```
It is faster and simpler than dict.setdefault()
use setdafaul:

```python
d = {}
for k, v in s:
       d.setdefault(k, []).append(v)
       print d.items()
```    

##global variable  
env.py  

```python
name = None
age = None
gender = None
```

run.py

```python
import env

def runner():
print name
print age
print gender
```

main.py

```python
import env
from run import runner
name = 'mike'
age = 21
gender = 'female'

runner()
```
