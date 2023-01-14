---
title: '[ffmpeg]-ffmpeg合并视频'
date: 2020-01-06 11:55:00
tags: ffmpeg
categories: ffmpeg
---

#### 1.方法一：FFmpeg concat协议
```
ffmpeg -i "concat:input1.mp4|input2.mp4|input3.mp4" -c copy output.mp4
```
#### 2.方法二：FFmpeg concat分离器
新建文件file.txt
```
file 'input1.mp4'
file 'input2.mp4'
file 'input3.mp4'
```
然后
```
ffmpeg -f concat -i file.txt -c copy output.mp4
```