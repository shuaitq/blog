---
title: "rle文件格式"
date: 2019-02-03
draft: false
tags: ["C++", "生命游戏", ".rle", "编码"]
categories: ["编程"]
author: "Tom Smith"
---

# 前言
之所以会了解到.rle文件，是因为在重构[Game-of-Life](https://github.com/shuaitq/Game-of-Life)的时候找到了一个关于生命游戏的Wiki：[LifeWiki](http://conwaylife.com/wiki/Main_Page)。在这个Wiki上面查阅了很多资料，发现这个网站关于生命游戏的资料非常全而且准确。然后在查着资料的时候突然注意到了在首页的一个下载图标

![](/image/download_pattern.png)

根据我对生命游戏的了解，当时猜测应该是类似保存生命的文件。然后就下载了下来看了看，发现里面有2000+个各式各样的生命游戏里的生命，而且都按照.rle文件格式保存，格式规范。于是我就决定抛弃之前使用手写json文件来输入生命初始状态的方法。毕竟支持了.rle就意味着有着一大堆开箱即用的生命（~~真香~~）

<!--more-->

# Run-length encoding主要思路

整个RLE的出发点用Wikipedia上的一个例子非常容易讲清楚，比如你现在有一个文件内容如下：

> WWWWWWWWWWWWBWWWWWWWWWWWWBBBWWWWWWWWWWWWWWWWWWWWWWWWBWWWWWWWWWWWWWW

你想要让它占用的空间尽可能的小且仍然保持人的可读性，然后你发现这个字符串只有英文字符而且字符串中的重复度非常的高，经常是很长一串的同一个字符。于是你就想出了以下的办法，把超过一个的重复字符用<数量><字符>来表示。于是字符串就变成了下面这个样子

> 12W1B12W3B24W1B14W

明显字符串长度减少了很多，而且仍然保持了人的可读性。整个RLE的主要思路就在这了。

# .rle文件的格式

```rle
#N Quasar
#O Robert Wainwright
#C A period 3 oscillator found in August 1971 that is quite similar to
#C the pulsar.
#C www.conwaylife.com/wiki/index.php?title=Quasar
x = 29, y = 29, rule = B3/S23
10b3o3b3o10b2$8bo4bobo4bo8b$8bo4bobo4bo8b$8bo4bobo4bo8b$10b3o3b3o10b2$
8b3o7b3o8b$2b3o2bo4bo3bo4bo2b3o2b$7bo4bo3bo4bo7b$o4bobo4bo3bo4bobo4bo$
o4bo17bo4bo$o4bo2b3o7b3o2bo4bo$2b3o19b3o2b2$2b3o19b3o2b$o4bo2b3o7b3o2b
o4bo$o4bo17bo4bo$o4bobo4bo3bo4bobo4bo$7bo4bo3bo4bo7b$2b3o2bo4bo3bo4bo
2b3o2b$8b3o7b3o8b2$10b3o3b3o10b$8bo4bobo4bo8b$8bo4bobo4bo8b$8bo4bobo4b
o8b2$10b3o3b3o!
```

.rle文件就是根据RLE思路设计的一个文件格式，上面是一个典型的.rle文件，其中主要可以分为三部分

* 注释区，所有以#开头的都是注释，在解析的时候直接丢弃就可以了。
* 头部区，在除掉注释的第一行就是头部，按照以下格式

```
x = 宽, y = 高, rule = 规则
```

* 内容区，按照RLE生成，b代表死细胞，o代表活细胞，$代表每行的结束。其中需要注意的是几点是
    1. 每行最后的死细胞直接忽略，不参与编码，因为不需要。
    2. 当重复的只有一个的时候忽略前面的数字1。
    3. 所有b、o、$都参与编码，即会出现2$这种情况，表示有一行全是死细胞。
    4. 最后以!标识内容区的结尾，后面的内容应该全部忽略。

# 坑

整个东西看懂之后还是很好实现的，就是规则部分可能是因为有太多软件使用到了这个格式作为导出格式。导致其中有一些软件夹带了一些私货或者不符合标准的东西在里面，为了能够兼容尽量多的.rle文件于是把所有在使用过程中遇到的格式都进行了兼容。

有以下几种：

```
x = 79, y = 21, rule = b3/s23
x = 7, y = 13, rule = B3/S23
x = 35, y = 52, rule = B3/S23:T35,52
x = 100, y = 100, rule = B3/S23:C100,100
x = 100, y = 100, rule = B3/S23:P100,100
x = 9, y = 11, rule = 23/3
x = 75, y = 61, rule = s23/b3
```

# 参考
* [Run Length Encoded - LifeWiki](http://conwaylife.com/wiki/RLE)
* [Run-leng`th encoding - Wikipedia](https://en.wikipedia.org/wiki/Run-length_encoding)