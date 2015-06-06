---
layout: post
title:  "工作记录 - pandas, python, css .."
categories: jekyll update
tags: workwork
---


##使用pandas对价格进行判断
### playground/pds.py
使用fiveNumber, 即箱线图的方式来区分异常价格

```python
from pymongo import MongoClient
from bson.objectid import ObjectId
import pandas as pd

# setUp 

prods = MongoClient('192.168.1.202')['dev'].products 
query = prods.find_one({'_id':ObjectId("53bce82e8ddf879f99703ee9")})
prices = query['_unitPrice']

def find_limits(describe, n = 1.5):
    '''find upper and lower limits from five number'''
    IQR = describe['75%'] - describe['25%']
    delta = n * IQR
    upper_limit = describe['75%'] + delta
    lower_limit = describe['25%'] - delta
    return lower_limit, upper_limit
#print(prices)
frame = pd.DataFrame(prices)
frame = frame.T
frame.columns = ['weight', 'price']
frame.price = frame.price.astype('int32')
de = frame.price.describe()
l, u = find_limits(de)
print(frame[ frame.price < l ])
print(frame[ frame.price > u ])
```

##nlp
###calculate the coverage of words from wordmaker
只有28.0%, 许多词太长了需要截短才能找到..

###src/caigen/nlp/commands/process_reviews.py

```python
from rq import Queue, Connecction, Worker
def worker():
    with Connecction(Redis()):
        w = Worker(Queue('review_nlp'))
        w.work()
```

##css

[visual formatting model](http://www.w3.org/TR/CSS21/visuren.html)  

[css tricks](http://css-tricks.com/)


##flatten 
```python
#yield from.. see PEP380
def walker(d):                                                                          
   if isinstance(d, dict):                                                             
       for k in d:                                                                     
           yield k                                                                     
           yield from walker(d[k])                                                     
d = {'a':1, 'b': {'c': 2, 'd': 'c'}}

#flatten nested list.
def flat(nested):
    try:
        for ele in nested:
            for subele in flatten_v3(ele):
                yield subele
    except TypeError:
        yield nested
```

##read PEP8 again 
[link](http://legacy.python.org/dev/peps/pep-0008/)


##Redis
redis is an in-memory remote database that offers high performance,
replication, and a unique data model to produce a platform for solving problems.

supporting five different types of data structs

```
#types
STRING
    set hello world
    get hello
LIST
    rpush list-key item1
    rpush list-key item2
    lrange list-key 0 -1
    lindex list-key 1
    lpop list-key
    lrange list-key 0 -1
SET
    sadd set-key item
    sadd set-key item2
    sadd set-key item3
    sadd set-key item //return (integer) 0
    smembers set-key
    sismember set-key item4
    sismember set-key item
    srem set-key item2
    smembers set-key
HASH
    hset hash-key sub-key1 value1
    hset hash-key sub-key2 value2
    hset hash-key sub-key1 value1
    hgetall hash-key
    hdel hash-key sub-key2
    hdel hash-key sub-key2
    hget hash-key sub-key1
    hgetall hash-key
ZSET
    zadd zset-key 728 member1
    zadd zset-key 982 member0
    zadd zset-key 982 member0
    zrange zset-key 0 -1 withscores
    zrange zset-key 0 800 withscores
    zrem zset-key member1
    zrem zset-key member1
    zrange zset-key 0 -1 withscores
```

##selenium
###simple usage
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Firefox()
driver.get("http://www.python.org")
assert "Python" in driver.title
elem = driver.find_element_by_name("q")
elem.send_keys("selenium")
elem.send_keys(Keys.RETURN)
driver.close()
```
###write tests
```python
import unittest
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

class PythonOrgSearch(unittest.TestCase):

    def setUp(self):
        self.driver = webdriver.Firefox()

    def test_search_in_python_org(self):
        driver = self.driver
        driver.get("http://www.python.org")
        self.assertIn("Python", driver.title)
        elem = driver.find_element_by_name("q")
        elem.send_keys("selenium")
        elem.send_keys(Keys.RETURN)

    def tearDown(self):
        self.driver.close()

if __name__ == "__main__":
    unittest.main()
```

###xpath
```python
element = browser.find_element_by_xpath("//select[@name='name']")
element = browser.find_element_by_xpath("//input[@id='kw1']")
```
if there's more than one element that matched the query, then only
    the first will be returned.If nothing can be found, a `NoSuchElementException`
    will be raised

###interacting with page
You can easily clear the contents of a text field or textarea with `clear` method:
```python
element.clear()
```

###filling in forms
`toggle` the state of drop down
use `setSelected` to set something like an OPTION tage selected
```python
##example
element = driver.find_element_by_xpath("//select[@name='name']")
all_options = element.find_elements_by_tag_name("option")
for option in all_options:
    print("Value is: %s" % option.get_attribute("value"))
    option.click()
```

```python
from selenium.webdriver.support.ui import Select
select = Select(driver.find_element_by_name('name'))
select.select_by_index(index)
select.select_by_visible_text("text")
select.select_by_value(value)

select = Select(driver.find_element_by_id('id'))
select.deselect_all()
```

###drag and drop
```python
element = driver.find_element_by_name("source")
target = driver.find_element_by_name("target")

from selenium.webdriver import ActionChains
action_chains = ActionChains(driver)
action_chains.drag_and_drop(element, target)
```

###moving between windows and frames
```python
driver.switch_to_window("windowName")
```

###notice
if your page uses a lot of ajax on load then WebDriver may not know
when it has completely loaded.If you need to ensure such pages
are fully loaded then you can use `waits`.


##worldcup
```python
from pyquery import PyQuery as pq
perfix = 'http://www.fifa.com'

def fetchStatistic(link, meta):
    rec = {}
    rec.update(meta)
    d = pq(link)
    generic = pq(d('#generalStatsTopContainer').html())
    stats = ['attacks', 'shots', 'delivery_penalty_area', 
    'clearances_attempted', 'passes_completed']
    for stat in stats:
        #print('basic', stat)
        home = generic('[data-codename={}] [data-statref=home]'.format(stat)).text().split(' ')[0]
        away = generic('[data-codename={}] [data-statref=away]'.format(stat)).text().split(' ')[0]
        rec[stat] = {'home': home, 'away': away}
        #print(home)
        #print(away)

    #for i, t in d('table.statistic-block  tr'):

    ###fouls
    fouls = d('#disciplinary .chart-container-donut-doubleside')
    home_fouls = fouls.children('.chart-leftlabel').text()
    away_fouls = fouls.children('.chart-rightlabel').text()
    foulStat = d('#disciplinary .table.statistics-block tr')
    for i,c in enumerate(foulStat):
        content = foulStat.eq(i).text()
        h, *fc, a = content.split(' ')
        #print(' '.join(fc), h, a)
        rec[' '.join(fc)] = {'home': h, 'away':a}
    print(rec)
    #TODO insert into mongodb

def main():
    d = pq('http://www.fifa.com/worldcup/matches/index.html')
    matches = d('.match-list-date .mu.result')
    for i, match in enumerate(matches):
        content = matches.eq(i).html()
        d = pq(content)  
        href = d('a').attr('href')
        date = d('.mu-i .mu-i-date').text()
        rund = d('.mu-i .mu-i-group').text()
        home = d('.mu-m .t.home .t-nText').text()
        Score = d('.mu-m .s div.s-score').text().split('-')
        homeScore = Score[0]
        awayScore = Score[1]
        away = d('.mu-m .t.away .t-nText').text()
        statisticLink = href.replace('index','statistics') 

        print(home, homeScore, away, awayScore)
        meta = {'game': {'home':home, 'away': away},
                'score':{'home':homeScore,'away':awayScore},
                'date': date,
                'round': rund}
        fetchStatistic(perfix + statisticLink, meta)
        break

if __name__ == "__main__":
    #fetchStatistic('aa')
    main()
```

##world cup player statistics
```
http://www.fifa.com/worldcup/matches/
and pdfs under each pages
```

##pdb commands
- h(elp)
- help command
- c(ontinue)
- q(uit)
- b(reak)number
- b path/to/file.py:number: set breakpoint at line No in specificc file
- s(tep) 
- n(ext)
- u(p) / d(own)
- a(rgs)
- debub statement
- l(ist) statement
- w(here)


##mongo search
```
$where
db.product.find(
    {"$where" :function(){
        for (var cur in this) {
            for (var other in this) {
            if (cur != other && this[cur] ==this[other])
            return true;
            }
        }
    return false
    }})
```
