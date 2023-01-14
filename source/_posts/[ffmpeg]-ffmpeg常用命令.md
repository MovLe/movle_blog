---
title: '[ffmpeg]-ffmpeg常用命令'
date: 2020-01-06 11:47:00
tags: ffmpeg
categories: ffmpeg
---

### 一.基础概念
#### 1.FFmpeg 的命令行参数非常多，可以分成五个部分。
```
ffmpeg {1} {2} -i {3} {4} {5}
```

上面命令中，五个部分的参数依次如下:
```
全局参数
输入文件参数
输入文件
输出文件参数
输出文件
```
#### 2.参数太多的时候，为了便于查看，ffmpeg 命令可以写成多行。
```
ffmpeg \
[全局参数] \
[输入文件参数] \
-i [输入文件] \
[输出文件参数] \
[输出文件]
```
下面是一个例子。
```
ffmpeg \
-y \ # 全局参数
-c:a libfdk_aac -c:v libx264 \ # 输入文件参数
-i input.mp4 \ # 输入文件
-c:v libvpx-vp9 -c:a libvorbis \ # 输出文件参数
output.webm # 输出文件
```
#### 3.FFmpeg 常用的命令行参数如下。
```
-c：指定编码器
-c copy：直接复制，不经过重新编码（这样比较快）
-c:v：指定视频编码器
-c:a：指定音频编码器
-i：指定输入文件
-an：去除音频流
-vn： 去除视频流
-preset：指定输出的视频质量，会影响文件的生成速度，有以下几个可用的值 ultrafast, superfast, veryfast, faster, fast, medium, slow, slower, veryslow。
-y：不经过确认，输出时直接覆盖同名文件。

主要参数：
-i 设定输入流
-f 设定输出格式
-ss 开始时间
视频参数：
-b 设定视频流量，默认为200Kbit/s
-r 设定帧速率，默认为25
-s 设定画面的宽与高
-aspect 设定画面的比例
-vn 不处理视频
-vcodec 设定视频编解码器，未设定时则使用与输入流相同的编解码器
音频参数：
-ar 设定采样率
-ac 设定声音的Channel数
-acodec 设定声音编解码器，未设定时则使用与输入流相同的编解码器
-an 不处理音频
```
### 二.常用命令
#### 1.视频画面上下翻转：
```
ffmpeg -i target.mp4 -vf vflip output.mp4
```
#### 2.左右翻转：
```
ffmpeg -i target.mp4 -vf hflip output.mp4
```
#### 3.画面顺时针旋转90度：

```
ffmpeg -i target.mp4 -vf transpose=1 output.mp4
```
#### 4.画面逆时针旋转90°：

```
ffmpeg -i target.mp4 -vf transpose=2 output.mp4
```
#### 5.查看文件信息
查看视频文件的元信息，比如编码格式和比特率，可以只使用-i参数。
```
ffprobe input.mp4
```
上面命令会输出很多冗余信息，加上-hide_banner参数，可以只显示元信息。
```
ffmpeg -i input.mp4 -hide_banner
```
#### 6.转换编码格式
转换编码格式（transcoding）指的是， 将视频文件从一种编码转成另一种编码。比如转成 H.264 编码，一般使用编码器libx264，所以只需指定输出文件的视频编码器即可。
```
ffmpeg -i [input.file] -c:v libx264 output.mp4
```
下面是转成 H.265 编码的写法。
```
ffmpeg -i [input.file] -c:v libx265 output.mp4
```
#### 7.转换容器格式
转换容器格式（transmuxing）指的是，将视频文件从一种容器转到另一种容器。下面是 mp4 转 webm 的写法。
```
ffmpeg -i input.mp4 -c copy output.webm
```
上面例子中，只是转一下容器，内部的编码格式不变，所以使用-c copy指定直接拷贝，不经过转码，这样比较快。

#### 8.调整码率
调整码率（transrating）指的是，改变编码的比特率，一般用来将视频文件的体积变小。下面的例子指定码率最小为964K，最大为3856K，缓冲区大小为 2000K。
```
ffmpeg \
-i input.mp4 \
-minrate 964K -maxrate 3856K -bufsize 2000K \
output.mp4
```
#### 9.改变分辨率（transsizing）
下面是改变视频分辨率（transsizing）的例子，从 1080p 转为 480p 。

```
ffmpeg \
-i input.mp4 \
-vf scale=480:-1 \
output.mp4
```
#### 10.提取音频
有时，需要从视频里面提取音频（demuxing），可以像下面这样写。
```
ffmpeg \
-i input.mp4 \
-vn -c:a copy \
output.aac
```
上面例子中，-vn表示去掉视频，-c:a copy表示不改变音频编码，直接拷贝。

#### 11.添加音轨
添加音轨（muxing）指的是，将外部音频加入视频，比如添加背景音乐或旁白。
```
ffmpeg \
-i input.aac -i input.mp4 \
output.mp4
```
上面例子中，有音频和视频两个输入文件，FFmpeg 会将它们合成为一个文件。

#### 12.截图
下面的例子是从指定时间开始，连续对1秒钟的视频进行截图。
```
ffmpeg \
-y \
-i input.mp4 \
-ss 00:01:24 -t 00:00:01 \
output_%3d.jpg
```
如果只需要截一张图，可以指定只截取一帧。
```
ffmpeg \
-ss 01:23:45 \
-i input \
-vframes 1 -q:v 2 \
output.jpg
```
上面例子中，-vframes 1指定只截取一帧，-q:v 2表示输出的图片质量，一般是1到5之间（1 为质量最高）。

#### 13.裁剪
裁剪（cutting）指的是，截取原始视频里面的一个片段，输出为一个新视频。可以指定开始时间（start）和持续时间（duration），也可以指定结束时间（end）。
```
ffmpeg -ss [start] -i [input] -t [duration] -c copy [output]
ffmpeg -ss [start] -i [input] -to [end] -c copy [output]
```
下面是实际的例子。
```
ffmpeg -ss 00:01:50 -i [input] -t 10.5 -c copy [output]
ffmpeg -ss 2.5 -i [input] -to 10 -c copy [output]
```
上面例子中，-c copy表示不改变音频和视频的编码格式，直接拷贝，这样会快很多。

#### 14.为音频添加封面
有些视频网站只允许上传视频文件。如果要上传音频文件，必须为音频添加封面，将其转为视频，然后上传。

下面命令可以将音频文件，转为带封面的视频文件。
```
ffmpeg \
-loop 1 \
-i cover.jpg -i input.mp3 \
-c:v libx264 -c:a aac -b:a 192k -shortest \
output.mp4
```
上面命令中，有两个输入文件，一个是封面图片cover.jpg，另一个是音频文件input.mp3。-loop 1参数表示图片无限循环，-shortest参数表示音频文件结束，输出视频就结束。
#### 15.压缩视频
```
ffmpeg -i target.mp4 -b:a 128k -b:v 7551k output.mp4
```