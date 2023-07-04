# c++高性能264/265实时h5流媒体服务器前后端整体解决方案

## 1.效果展示

下图展示了前端播放效果。

+ 播放1路264视频流，4路265视频流

+ CPU占用率10%(测试机器上运行着c++服务端和其他工具程序)

+ GPU0占用率17%

![5channel-gpu-cpu](https://github.com/7956968/h5server/blob/master/preview/5channel-gpu-cpu-stop.png)
![5channel-gpu-cpu](https://github.com/7956968/h5server/blob/master/preview/5channel-gpu-cpu.png)

### 1.1  作者测试机器配置

处理器	11th Gen Intel(R) Core(TM) i7-11800H @ 2.30GHz   2.30 GHz
机带 RAM	32.0 GB (31.6 GB 可用)
系统类型	WIN10 64 位操作系统, 基于 x64 的处理器
笔和触控	没有可用于此显示器的笔或触控输入
显示设备    GPU-0 : 集成显示芯片 GPU-1:RTX3070 


## 2.特点

+ 利用新版chrome特性，无论是264还是265，都基于GPU硬件解码，性能卓越，可轻松实现16路/甚至64路 265视频播放,和本地播放器无差异,得益于chrome优秀的视频解码能力，甚至比很多本地播放器做的很好；
+ websocket传输实时流，极低延迟；
+ 前端代码基于h5原始“video”标签，方便集成到自己的前端项目中，并且提供简单播放器UI，方便修改，实现自己的前端逻辑；
+ 纯c++编写， 要求c++版本不低于14，少量c++14特性，具体特性见“4. 技术方案以及第三方库介绍 c++一栏”；
+ c/c++独有的高效内存使用,尽可能的避免内存拷贝；
+ 代码风格严格遵守google规范,从头文件包含顺序、变量命名到函数逻辑，遵循“一个函数只做一件事情”，谨慎对待每个警告，附上源码截图，文件列表截图。如有需要可提供单元测试代码，方便集成到自己的ci/cd环境中；
+ 跨平台，兼容window/linux, 可以运行在绝大多数嵌入式、x86环境下；
+ 媒体源为原始264/265裸流，方便集成到自己硬件环境中，如果采用mp4做源, ffmpeg读取，使用者需要额外考虑如何组帧、如何添加时间戳；

## 3.功能框架

## 4. 技术方案以及第三方库介绍
| 组件名称 |说明|源码地址|
| -------- |--------|--------|
| c++ | c++11 function、lambda等，c++14 optional ||
| 网络库 | 基于yasio/xxsocket修改 |https://github.com/yasio/yasio|
| websocket库 | wslay|https://github.com/tatsuhiro-t/wslay|
| flv打包 |libflv|https://github.com/ireader/media-server|
| 264/265码流分析 |抽出webrtc库中的一部分做成库libvideo_stream_parser|https://github.com/chromium/chromium|
| 日志库 |loguru|https://github.com/emilk/loguru|
|字符串分析库|pystring|https://github.com/imageworks/pystring|
|前端库|mpegts.js|https://github.com/xqq/mpegts.js|
## 5. 如何编译
### 5.1 windows环境

#### 5.1.1 安装mingw64

+ 下载mingw
推荐版本：x86_64-7.3.0-release-posix-seh-rt_v5-rev0.7z
推荐地址：https://mirrors.cloud.tencent.com/qt/development_releases/prebuilt/mingw_64/
+ 下载后不用安装，解压缩到“C:\x86_64-7.3.0-release-posix-seh-rt_v5-rev0”
+ 新增path环境变量:“C:\x86_64-7.3.0-release-posix-seh-rt_v5-rev0\mingw64\bin”
+ 打开windows命令行，输入gcc -v, 确认gcc版本号gcc version 7.3.0

#### 5.1.2 安装cmake
+ 下载windows版本cmake，要求cmake版本不低于2.8
+ 设置path环境变量

#### 5.1.2 正式编译
+ mkdir build
+ cd build
+ cmake -G "MinGW Makefiles" -D CMAKE_CXX_COMPILER="g++.exe" -D CMAKE_C_COMPILER="gcc.exe" ..
+ mingw32-make.exe
+ 完成

### 5.2 linux x86环境

同 5.1.2步骤

### 5.1.3  linux 交叉编译请在购买源码后联系作者
## 6 如何运行

简短的说，

+ 直接运行包中的主程序wserver.exe，

+ 打开浏览器http://127.0.0.1:8086/

如果没遇到问题，可以跳过此章节

### 6.1 目标文件目录结构

├── libgcc_s_seh-1.dll
├── libstdc++-6.dll
├── libwinpthread-1.dll
├── live_stream
├── ReadMe.pdf
├── web
├── wserver.exe
└── wssconfig.json

| 名称|意义|
| -------- |--------|
| libgcc_s_seh-1.dll、 libstdc++-6.dll、libwinpthread-1.dll| mingw64运行库 |
| wserver.exe |主程序 |
| wssconfig.json | 配置文件 |
| web |网页目录 |
| live_stream |媒体流文件目录 |


### 6.2 配置文件
#### 配置文件字段列表
配置文件为wssconfig.json
各字段意义如下：

| 名称|类型 |意义|默认值|
| -------- |--------| -------- |
| websocket_server_port | 数字 |websocket服务端口 |8894|
| websocket_server_bind_addr | 字符串 |websocket服务绑定网卡地址 |"0.0.0.0"|
| http_server_port | 数字 |http服务端口 |8086|
| http_server_bind_addr |字符串 | http服务绑定网卡地址|"0.0.0.0"|
#### 隐含配置
程序当前目录 “live_stream”和"web"文件夹分别存放视频文件和网页

### 6.3 运行方式
#### 6.3.1不带配置文件启动，默认配置， 直接运行可执行程序, ./wserver
#### 6.3.2 带配置文件启动 ./wserver wssconfig.json

注意：***前端代码中websocket端口写了websocket端口8894，如果修改此配置，需要同时修改前端代码***


### 6.5 打开浏览器播放

http://127.0.0.1:8086/

## 7 如何测试我的视频
+ 将视频放在程序提示的video目录，注意***视频文件以及文件路径名称不能包含中文*** 
+ 打开前端网页，可能要求刷新一次，看到视频出现在列表中
+ 单个视频文件大小不能大于300MB

## 8  如何获得测试视频
用ffmpeg将视频转换为hevc，注意：***实时流无法播放带b帧的数据***，"-bf 0" 这个参数意味着得到的视频不包含b帧，示例命令如下：
ffmpeg.exe -i 109.264 -bf 0 109.hevc

## 9 联系方式：7956968@qq.com, vx:zoopmss

