　　ffmpeg不需要单独装，装mpv就会把它顺带也装上。它包含三个工具，分别是ffprobe、ffplay、ffmpeg。

## ffprobe

　　这是一个用来<span class="orange">查看视频信息</span>的工具，命令为`ffprobe 文件名`，输出如下所示：
```shell
$ ffprobe ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts 
ffprobe version n4.2 Copyright (c) 2007-2019 the FFmpeg developers
    built with gcc 9.1.0 (GCC)
    configuration: --prefix=/usr --disable-debug --disable-static --disable-stripping --enable-fontconfig --enable-gmp --enable-gnutls --enable-gpl --enable-ladspa --enable-libaom --enable-libass --enable-libbluray --enable-libdav1d --enable-libdrm --enable-libfreetype --enable-libfribidi --enable-libgsm --enable-libiec61883 --enable-libjack --enable-libmodplug --enable-libmp3lame --enable-libopencore_amrnb --enable-libopencore_amrwb --enable-libopenjpeg --enable-libopus --enable-libpulse --enable-libsoxr --enable-libspeex --enable-libssh --enable-libtheora --enable-libv4l2 --enable-libvidstab --enable-libvorbis --enable-libvpx --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxcb --enable-libxml2 --enable-libxvid --enable-nvdec --enable-nvenc --enable-omx --enable-shared --enable-version3
    libavutil      56. 31.100 / 56. 31.100
    libavcodec     58. 54.100 / 58. 54.100
    libavformat    58. 29.100 / 58. 29.100
    libavdevice    58.  8.100 / 58.  8.100
    libavfilter     7. 57.100 /  7. 57.100
    libswscale      5.  5.100 /  5.  5.100
    libswresample   3.  5.100 /  3.  5.100
    libpostproc    55.  5.100 / 55.  5.100
Input #0, mpegts, from '/home/murongxixi/Videos/HNTV-谭晶《九儿》[我是歌手].ts':
    Duration: 00:05:01.03, start: 1963.431211, bitrate: 16201 kb/s
    Program 3601 
        Stream #0:0[0xb10]: Video: mpeg2video (Main) ([2][0][0][0] / 0x0002), yuv420p(tv, top first), 1920x1080 [SAR 1:1 DAR 16:9], 16000 kb/s, 25 fps, 25 tbr, 90k tbn, 50 tbc
        Stream #0:1[0xb13](eng): Audio: ac3 ([6][0][0][0] / 0x0006), 48000 Hz, 5.1(side), fltp, 320 kb/s
        Stream #0:2[0xb14](eng): Audio: mp2 ([3][0][0][0] / 0x0003), 48000 Hz, stereo, fltp, 128 kb/s
```
其中第3行是它的<span class="orange">版本信息</span>，第4行是它的<span class="orange">编译信息</span>，第5行是<span class="orange">编译时的选项</span>，分别开启/关闭了哪些功能，第6-13行是它<span class="orange">各个组件的版本号</span>。这些信息与视频文件无关，如果不想看到它们只需加上`-hide_banner`即可：
```shell
$ ffprobe -hide_banner ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts 
Input #0, mpegts, from '/home/murongxixi/Videos/HNTV-谭晶《九儿》[我是歌手].ts':
    Duration: 00:05:01.03, start: 1963.431211, bitrate: 16201 kb/s
    Program 3601 
        Stream #0:0[0xb10]: Video: mpeg2video (Main) ([2][0][0][0] / 0x0002), yuv420p(tv, top first), 1920x1080 [SAR 1:1 DAR 16:9], 16000 kb/s, 25 fps, 25 tbr, 90k tbn, 50 tbc
        Stream #0:1[0xb13](eng): Audio: ac3 ([6][0][0][0] / 0x0006), 48000 Hz, 5.1(side), fltp, 320 kb/s
        Stream #0:2[0xb14](eng): Audio: mp2 ([3][0][0][0] / 0x0003), 48000 Hz, stereo, fltp, 128 kb/s
```
其中`Duration: 00:05:01.03`是时长，`bitrate: 16201 kb/s`是码率，三行Stream表明有三个轨道(默认<span class="orange">先视频后音频</span>，<span class="orange">从0开始编号</span>)：
- 0号轨道`Stream #0:0`是视频轨，编码格式`mpeg2video`，颜色空间`yuv`，采样`420`，隔行编码顶场优先`top first`，分辨率`1920x1080`，显示宽高比`16:9`，码率`16000 kb/s`，帧率`25 fps`。
- 1号轨道`Stream #0:1`是第一条音频轨，编码格式`ac3`，`5.1`声道，码率`320 kb/s`。
- 2号轨道`Stream #0:2`是第二条音频轨，编码格式`mp2`，`stereo`声道，码率`128 kb/s`。

## ffplay

　　这是一个用来<span class="orange">播放视频</span>的工具，命令为`ffplay -hide_banner ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts`，输出如下所示：
![ffplay](https://raw.githubusercontent.com/murongxixi/Arch/master/img/ffplay.png)
此外还能进行**调试**，命令为`ffplay -hide_banner -flags2 +export_mvs ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -vf codecview=mv=pf+bf+bb`，输出如下所示：
![ffplay-debug](https://raw.githubusercontent.com/murongxixi/Arch/master/img/ffplay-debug.png)
密密麻麻的小箭头记录了帧之间的运动方向(突然发现这张图真是理解<span class="orange">向量场</span>的好图)。

## ffmpeg

　　<span class="orange">单独抽出每条轨道</span>：
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -map 0:0 -c copy a.mpeg  # 视频轨
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -map 0:1 -c copy a.ac3   # 第一条音频轨
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -map 0:2 -c copy a.mp2   # 第二条音频轨
```

其中`-map 0:*`具体对应哪条轨道参考上面ffprobe的输出，`-c copy`表明对轨道直接复制，不重新编码。由于该视频只有一个视频轨，因此也可以通过如下命令抽取：
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -an -c copy a.mpeg  # 视频轨
```

其中`-an`表示<span class="orange">忽略音频部分</span>(audio neglect)，类似的如下命令可以抽出第一条音频轨：
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -vn -c copy a.ac3  # 第一条音频轨
```
其中`-vn`表示<span class="orange">忽略视频部分</span>(该方法无法抽第二条音频轨)。

　　不重新编码，<span class="orange">更换封装格式</span>
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -c copy o.vob  # 换成vob封装格式
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -c copy o.mp4  # 换成mp4封装格式
```
换封装的前提是：<span class="orange">目标封装格式支持原来的的视频音频编码</span>，比如原视频的音轨是ac3编码的，那它就没法用mp4重新封装，因为mp4不支持ac3音轨。

　　<span class="orange">删除</span>ac3这条<span class="orange">音频轨</span>：
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -map 0:0 -map 0:2 -c copy o.ts
```

　　`-map`还能<span class="orange">跨文件合并</span>，例如想将a.mp4的视频(假设是0号轨道)与b.mp4的音频(假设是2号轨道)合并，可以通过通过如下命令实现：
```shell
ffmpeg -hide_banner -i a.mp4 -i b.mp4 -map 0:0 -map 1:2 -c copy o.mp4
```
即`-i`后面的文件从0开始编号，`-map 1:2`表示1号文件的2号轨道。

　　<span class="orange">截取视频</span>的第1分钟到第3分钟：
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -ss 00:01:00.000 -to 00:03:00.000 -c copy o.ts
```

　　<span class="orange">连接多个视频</span>，针对早期的DVD碟(一堆.vob文件)或者被分割的在线视频。假设有3个视频，分别是1.mp4、2.mp4、3.mp4，只需执行如下命令即可：
```shell
echo file '1.mp4' > filelist.txt
echo file '2.mp4' >> filelist.txt
echo file '3.mp4' >> filelist.txt
ffmpeg -hide_banner -f concat -i filelist.txt -c copy o.mp4
```

　　<span class="orange">调整视频分辨率</span>，长宽均缩小为原来的一半，以h264重新编码输出，编码参数为`-qp 0`：
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -vf "scale=0.5*in_w:0.5*in_h" -c:v libx264 -qp 0 -an o.264
```
要想先预览效果可以用前面的ffplay：
```shell
ffplay -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -vf "scale=0.5*in_w:0.5*in_h"
```

　　<span class="orange">添加图片水印</span>，水印位置通过overlay设置：
```shell
ffmpeg -hide_banner -i a.mp4 -i "logo.png" -filter_complex "overlay=120:120" -c:a copy -c:v libx264 -crf 0 -preset slow -tune film o.mp4
```

　　<span class="orange">调整亮度</span>，参数`1.6`可调：
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -vf "lutyuv=y=val*1.6" -c:v libx264 -qp 0 -an o.264
```
ffplay预览：
```shell
ffplay -hide_banner ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ts -vf "lutyuv=y=val*1.6"
```
![亮度](https://raw.githubusercontent.com/murongxixi/Arch/master/img/volume.png)

　　视频重编码一般不用ffmpeg，<span class="orange">音频重编码</span>：
```shell
# -ar表示采样率 -b:a表示码率
ffmpeg -hide_banner -i input_file -vn -c:a aac -ar 48000 -b:a 320k o.m4a  # 重编码为aac
ffmpeg -hide_banner -i input_file -vn -c:a flac o.flac                    # 重编码为flac
ffmpeg -hide_banner -i input_file -vn -c:a alac o.m4a                     # 重编码为alac
```

　　<span class="orange">查看频谱</span>，横轴频率，纵轴时间，第一张图是九儿ts视频里的ac3，到15000左右就截断了；第二张是无损的九儿flac，频谱就非常饱满：
```shell
ffmpeg -hide_banner -i ~/Videos/HNTV-谭晶《九儿》\[我是歌手\].ac3 -lavfi showspectrumpic=s=960x540:orientation=1 spectrum1.png
```
![ac3频谱](https://raw.githubusercontent.com/murongxixi/Arch/master/img/spectrum1.png)
```shell
ffmpeg -hide_banner -i 谭晶\ 歌手\ 02\ 九儿.flac -lavfi showspectrumpic=s=960x540:orientation=1 spectrum2.png
```
![flac频谱](https://raw.githubusercontent.com/murongxixi/Arch/master/img/spectrum2.png)

　　<span class="orange">增大音量</span>，参数`2`可调：
```shell
ffmpeg -hide_banner -i input_file -af 'volume=2' output_file
```
