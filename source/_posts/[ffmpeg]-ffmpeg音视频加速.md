---
title: '[ffmpeg]-ffmpeg音视频加速'
date: 2020-01-22 11:55:00
tags: ffmpeg
categories: ffmpeg
---
#### 1.变速视频原理
修改视频的pts，dts

#### 2.修改视频速率
视频变为2倍速
```
ffmpeg -i input.mp4 -an -filter:v "setpts=0.5*PTS" output.mp4
```
注意：

* 调整速度倍率范围[0.25, 4]
* 如果只调整视频的话最好把音频禁掉
* 对视频进行加速时，如果不想丢帧，可以用-r 参数指定输出视频FPS

```
ffmpeg -i input.mp4 -an -r 60 -filter:v "setpts=2.0*PTS" output.mp4
```

#### 3.变速音频原理
简单的方法是调整音频采样率，但是这种方法会改变音色，一般采用通过对原音进行冲采样，差值等方法

#### 4.修改音频速率

```
ffmpeg -i input.mp4 -filter:a "atempo=2.0" -vn output.mp4
```

注意：

* 倍率调整范围为[0.5, 2.0]
* 如果需要调整4倍可采用以下方法：

```
ffmpeg -i input.mp4 -filter:a "atempo=2.0,atempo=2.0" -vn output.mp4
```

#### 5.音频视频同时变速

```
ffmpeg -i input.mp4 -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" output.mp4
```
