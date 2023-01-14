---
title: 'MapReduce之OutputFormat数据输出'
date: 2019-01-01 17:56:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
#### 1.OutputFormat接口实现类
&nbsp;&nbsp;&nbsp;&nbsp;OutputFormat是MapReduce输出的基类，所有实现MapReduce输出都实现了 OutputFormat接口。下面我们介绍几种常见的OutputFormat实现类。

##### (1).文本输出TextOutputFormat
&nbsp;&nbsp;&nbsp;&nbsp;默认的输出格式是TextOutputFormat，它把每条记录写为文本行。它的键和值可以是任意类型，因为TextOutputFormat调用toString()方法把它们转换为字符串。

##### (2).SequenceFileOutputFormat
&nbsp;&nbsp;&nbsp;&nbsp;SequenceFileOutputFormat将它的输出写为一个顺序文件。如果输出需要作为后续 MapReduce任务的输入，这便是一种好的输出格式，因为它的格式紧凑，很容易被压缩。
##### (3).自定义OutputFormat

&nbsp;&nbsp;&nbsp;&nbsp;根据用户需求，自定义实现输出。
#### 2.自定义OutputFormat

&nbsp;&nbsp;&nbsp;&nbsp;为了实现控制最终文件的输出路径，可以自定义OutputFormat。
&nbsp;&nbsp;&nbsp;&nbsp;要在一个mapreduce程序中根据数据的不同输出两类结果到不同目录，这类灵活的输出需求可以通过自定义outputformat来实现。
##### (1).自定义OutputFormat步骤
(a)自定义一个类继承FileOutputFormat。
(b)改写recordwriter，具体改写输出数据的方法write()。
##### (2).实操案例：