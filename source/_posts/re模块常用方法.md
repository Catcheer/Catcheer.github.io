---
title: python3re模块常用方法
date: 2019-04-17 15:14:18
tags:
---



### match 
从字符串开始匹配
  
   + 如果匹配不成功返回None
   + 如果匹配成功则返回对象,对象可以调用group()
   + m.group()返回整个匹配对象.
   + m.group(n)则返回第n个子组匹配.若无此子组则返回空元组

### search

从字符串任意位置开始匹配

匹配结果同match

<!--more-->
### sub
  sub可以用于字符串替换
  ```
  re.sub(reg,rstr,str)
  ```
  其中rstr可用\n表示匹配的第n个分组

### split

分割字符串.字符串也有该方法,使用时应优选用字符串split方法.

re.split除了接受正则表达式外,还可以接受一个函数.函数返回值会作为分割字符串的字符

### findall(pattern, string, flags=0)

对 string 返回一个不重复的 pattern 的匹配列表， string 从左到右进行扫描，匹配按找到的顺序返回。如果样式里存在一到多个组，就返回一个组合列表；就是一个元组的列表（如果样式里有超过一个组合的话）。空匹配也会包含在结果里