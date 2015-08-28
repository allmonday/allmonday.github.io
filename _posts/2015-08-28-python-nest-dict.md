---
layout: post
title:  "python 读取嵌套字典"
categories: python
tags: python nested
---

## 场景

```
# 假设我们有: 
Val = {'a': {'b': {'c': 1}}}
```

最佳方案: 
```python
Val.get('a', {}).get('b', {}).get('c', None)
```

显然我在开玩笑
  
## 试试 `dget`吧
  
```python
def dget(dictionary, cmd, default=None):
    cmd_list = cmd.split('.')
    tmp = dict(dictionary)
    for c in cmd_list:
        try:
            val = tmp.get(c, None)
        except AttributeError:
            return default
        if val!= None:
            tmp = val
        else:
            return default
    return tmp


if __name__ == "__main__":
    data = {'a':{'b':{'c':1}}}
    dget(data, 'a.b.c') # 1
    dget(data, 'a.d.e') # None
    dget(data, 'a.b') # {'c': 1}
```
