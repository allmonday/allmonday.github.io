---
layout: post
title:  "python decorator"
categories: jekyll update
tags: 重构完成
---

##使用decorator的起因

为函数添加附加功能,但又要做的简洁优雅
###首先,写一个函数,输出`hello`

```python
def say():
    return 'hello'
print say()
```

###然后用一个decorator, 输出`~hello~`

```python
def hug(fn):
    def wrap():
        return '~' + fn() + '~'
    return wrap    

@hug
def say():
    return 'hello'
print say()
```

```python
# 类的写法
class hub(object):
    def __init__(self, fn):
        self.fn = fn
    def __call__(self):
        return '~' + self.fn() + '~'

@hug
def say():
    return 'hello'
print say()
```

##更好地使用decorator
对带参数的函数的话呢? 也一样
###我要say可以带参数,say('sleep') =>`~sleep~`

```python 
def hug(fn):
    def wrap(content): #same argument with wrapped func
        return '~' + fn(content) + '~'
    return wrap

@hug
def say(content):
    return content
print say('sleep')
```

```python 
# 类的写法
class wrap(object):
    def __init__(self, fn):
        self.fn = fn
    def __call__(self, word):
        return '~' + self.fn(word) + '~'
@wrap
def say(word):
    return word
print say('hello')
```

##更灵活地使用decorator
函数带参数没问题了,那要是装饰器带函数呢？
###我想decorator可以带参数, 这样就能把~换成任意想要的字符了

```python
def macrohug(character):  
    def hug(fn):          
        def wrap(content):
            return character + fn(content) + character  
        return wrap
    return hug

@macrohug('*')
def say(content):
    return content

print say('sleep')
```

```python
# 类的写法
class hug(object):
    def __init__(self, character):
        self.char = character
    def __call__(self, fn):
        def wrap(word):
            return self.char + fn(word) + self.char
        return wrap

@hug('*')
#foo = wrap(foo)
def say(word):
    return word
print say('hehe')
```

我想要 ~\*sleep\*~的效果！ 
###嵌套
```python
def macrohug(character):  #<==fetch argument.
    def hug(fn):          
        def wrap(content):
            return character + fn(content) + character   #<==variable
        return wrap
    return hug

@macrohug('~')
@macrohug('*')
def say(content):
    return content
print say('sleep')
```

##decorator可遇到的问题
`say.__name__` 变成了`wrap`.. 这样可不太好
###使用wraps保持函数原始样貌
```python
from functools import wraps
def macrohug(character):
    def hug(fn):
        @wraps(fn)
        def wrap(*args, **kwargs):
            return character + fn(*args, **kwargs) + character
        return wrap
    return hug

@macrohug('~')
@macrohug('*')
def say(content):
    return content
print say('sleep')
print say.__name__
```

```python
from functools import wraps
class hug(object):
    def __init__(self, character):
        self.char = character
    def __call__(self, fn):
        @wraps(fn)
        def wrap(word):
            return self.char + fn(word) + self.char
        return wrap

@hug('*')
@hug('*')
#foo = wrap(foo)
def say(word):
    return word

print say('hehe')
print say.__name__
```
