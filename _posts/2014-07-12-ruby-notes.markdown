---
layout: post
title:  "你好 Ruby"
categories: jekyll update
tags: 未完待续
---

##ruby Hash

{% highlight ruby %}
ratings = Hash.new 0
ratings['Harry Porter'] += 1
{% endhighlight %}
和python比较  
{% highlight python %}
from collections import defaultdict
a = {}
a = defaultdict(lambda:0,a)
a['Harry Porter'] += 1

#another way
class Counter(dict):
	def __missing__(self, key):
		return 0
c = Counter()
c['a'] += 1
{% endhighlight %}

##times
{% highlight ruby %}
5.times { print 'hello world'}

{% endhighlight %}

##directories and files

{% highlight ruby %}
Dir.entries "/"
Dir.entries "."
Dir["t.py"]
print File.read("t.py")
FileUtils.cp("/comics.txt", "/Home/comics.txt")

File.open("/Home/comics.txt", "a") do |f|
	f << "Cat and Girl"
end
FIle.mtime("/Home/comics")
FIle.mtime("/Home/comics").hour
{% endhighlight %}


##function

{% highlight ruby %}
def load_weather(path)
	days = {}
	File.foreach(path) do |line|
		day, weather = line.split(': ')
		days[day] = weather
	end
	days
end
{% endhighlight %}

##require

{% highlight ruby %}
require 'popup'
{% endhighlight %}

##class
{% highlight ruby %}
class BlogEntry
	attr_accessor :title, :time, :fulltext, :mood
end
entry = BlogEntry.new
entry.title = "today's moive"
entry.time = Time.now
entry.mood = :sick

class BlogEntry
	def initialize(title, mood, fulltext)
		@time = Time.now
		@title, @mood, @fulltext = title, mood, fulltext
	end
end
{% endhighlight %}

##Rails
{% highlight ruby %}
#find by id = 1
Zombie.find(1)
#create
Zombie.create
#find last
Zombie.last
#find all order by names
Zombie.all.order(:name)
#update
Zombie.find(1).update(name:'alibaba')
#delete
Zombie.find(1).delete
{% endhighlight %}

##inherit
python 的新式类的继承方式与ruby类似
