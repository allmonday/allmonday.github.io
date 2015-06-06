---
layout: post
title:  "linux commands tips"
categories: jekyll update
tags: 卧槽施工中
---


## shell
```
cat /etc/shells
echo $SHELL
chsh -s /bin/bash
chsh -s /bin/zsh
```

```
for i in 1 2 3 4 5
    do
        echo "welcome $i times"
    done
```

```
.bash_profile   #if things doesn't work in .bashrc, check here..
export thing=value   #should be written in .bashrc 
```
## tar  

```
tar -zcvf file.tar.gz filedirectory 
tar -zxvf file.tar.gz  
```
[参考](http://www.cnblogs.com/jyaray/archive/2011/04/30/2033362.html)

## ls  
```
ls -alh  #30000000 -> 30M
```

## awk
```
awk '!a[$0]++' file   #remove duplicated lines
awk 'NR!=1{print > $6}' target.txt   #折分文件
awk '!a[$0]++' file   # 去除重复行
```

## sort
```                              
sort -R input | head -n 100 >output   #select random lines from a file
```

## vim
```
ctrl + w + x   #交换窗口
```
## python - lib
```
#sklearn 安装走投无路时
pip3 install scikit-learn
pip3 install git+https://github.com/scikit-learn/scikit-learn.git
```

```
#gensim 的3.3版本需要从github上安装
git clone git@github.com:piskvorky/gensim
```
## GIT
```
git config --global color.ui auto    #色彩
```


## mongo copy & past
```
set -x                                                                                      
for i in "products" "brandSwitches"                                                         
do                                                                                          
    mongo etl-dev --eval "db.$i.drop()"                                                     
    mongoexport -h 192.168.1.202 -d dev -c $i | mongoimport -d etl-dev -c $i                
done                                                                                        
```
