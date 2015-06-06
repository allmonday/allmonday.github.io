---
layout: post
title:  "Jekyll's simple tutorial"
date:   2014-04-30 22:24:28
categories: jekyll update
---

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

Jekyll also offers powerful support for code snippets:

for ruby:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

for python:

{% highlight python linenos %}
#coding=utf-8
import glob
for i in glob.glob("*.*"):
	print i
{% endhighlight %}

some templates:  
time : {{ site.time | date_to_xmlschema }}


Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].  
[next-post]({% post_url 2014-05-10-badminton %})  
{% gist allmonday/646a6f153b5146aa6714 %}



[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
