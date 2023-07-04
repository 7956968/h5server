
# c++ hight performance 264/265 live h5 media server front & back end solution


# README.md
- en [English](ReadMe.md)
- zh_CN [简体中文](readme/README.zh_CN.md)

## 1.Preview

The following figure shows the front-end playback effect。

+ Play 1 channel 264 video stream, 4 channel 265 video stream

+ 10% CPU usage(C++ server and other tool programs are running on the test machine)

+ GPU0 occupancy rate is 17%

![5channel-gpu-cpu](https://github.com/7956968/h5server/blob/master/preview/5channel-gpu-cpu-stop.png)
![5channel-gpu-cpu](https://github.com/7956968/h5server/blob/master/preview/5channel-gpu-cpu.png)

### 1.1  test machine configuration

CPU	11th Gen Intel(R) Core(TM) i7-11800H @ 2.30GHz   2.30 GHz
RAM	32.0 GB 
GPU    GPU-0 : Integrated display chip GPU-1:RTX3070 


## 2.features

+ Using the new version of chrome features, whether it is 264 or 265, it is based on GPU hardware decoding, with excellent performance, and can easily realize 16-channel or even 64-channel 265 video playback, which is no different from local players. Thanks to chrome's excellent video decoding capabilities, Even better than many local players；
+ Websocket transmits real-time streams with extremely low latency；
+ The front-end code is based on the original h5 "video" tag, which is easy to integrate into your own front-end project, and provides a simple player UI for easy modification and realization of your own front-end logic；
+ Written in pure C++, the C++ version is required to be no less than 14, and a small number of C++14 features. For specific features, see "4. Technical solutions and third-party library introduction C++ column"；
+ c/c++'s unique high-efficiency memory usage, avoiding memory copying as much as possible；
+ The code style strictly abides by the Google specification, from header file inclusion order, variable naming to function logic, follows "a function only does one thing", and treats every warning with caution. If necessary, unit test code can be provided for easy integration into your own ci/cd environment；
+ Cross-platform, compatible with window/linux, can run in most embedded and x86 environments；
+ The media source is the original 264/265 bare stream, which is convenient to integrate into your own hardware environment. If you use mp4 as the source and read it with ffmpeg, users need to consider how to frame and add time stamps；

## 3.functional framework

## 4. Technical solution and third-party library introduction
| component name |illustrate|source |
| -------- |--------|--------|
| c++ | c++11 function、lambda等，c++14 optional ||
| network| 基于yasio/xxsocket修改 |https://github.com/yasio/yasio|
| websocket | wslay|https://github.com/tatsuhiro-t/wslay|
| flv |libflv|https://github.com/ireader/media-server|
| 264/265 stream parser |base webrtc,libvideo_stream_parser|https://github.com/chromium/chromium|
| log |loguru|https://github.com/emilk/loguru|
|string|pystring|https://github.com/imageworks/pystring|
|front ended|mpegts.js|https://github.com/xqq/mpegts.js|


## 9 contact：7956968@qq.com, vx:zoopmss
