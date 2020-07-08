重启进入系统后，还有大量的软件要安装

<br>

驱动
```shell
sudo pacman -S intel-ucode intel-media-driver intel-compute-runtime
sudo pacman -S libva-vdpau-driver mesa-vdpau libvdpau-va-gl vdpauinfo
sudo pacman -S nvidia-dkms nvidia-utils opencl-nvidia
sudo pacman -S bumblebee primus
sudo systemctl enable bumblebeed.service
sudo usermod -aG bumblebee murongxixi
```

<br>

工具
```shell
sudo pacman -S betterlockscreen                     # 锁屏
sudo pacman -S curl wget youtube-dl transmission-qt # 下载
yay -S xdman                                        # 下载
sudo pacman -S v2ray qv2ray-dev-git                 # 科学上网
sudo pacman -S mousepad                             # 文本编辑器
sudo pacman -S geogebra                             # 画图
sudo pacman -S deepin-screen-recorder               # 截图
sudo pacman -S deepin-image-viewer                  # 看图
sudo pacman -S deepin-picker                        # 取色
sudo pacman -S baidupcs-go-git baidunetdisk-bin     # 度盘
sudo pacman -S electronic-wechat deepin-wine-tim    # 微信 qq
```

<br>

文档
```shell
# wps
sudo pacman -S wps-office-cn wps-office-mime-cn wps-office-mui-zh-cn ttf-wps-fonts

# latex 如果不在乎节省硬盘空间可以直接 pacman -S texlive-most texlive-lang
sudo pacman -S texlive-core texlive-latexextra texlive-science texlive-pictures texlive-fontsextra texlive-langchinese

# latexindent依赖的perl库
sudo pacman -S perl-log-log4perl perl-yaml-tiny perl-file-homedir perl-unicode-linebreak
yay -S perl-log-dispatch

# pdf
sudo pacman -S evince pdftk
yay -S masterpdfeditor-free

# markdown编辑
sudo pacman -S typora pandoc pandoc-citeproc pandoc-crossref princexml

# markdown词典
sudo pacman -S hunspell hunspell-en_AU hunspell-en_CA hunspell-en_GB hunspell-en_US

# 博客
sudo pacman -S hugo
sudo pacman -S npm
sudo npm install -g gitbook-cli # aur里的包都过期了
```

<br>

娱乐
```shell
sudo pacman -S ghostscript inkscape imagemagick pinta krita                              # 图片
sudo pacman -S mpv mpv-bash-completion-git vlc gpac mediainfo-gui mkvtoolnix-gui aegisub # 视频
sudo pacman -S audacious mpg123 spek                                                     # 音乐
sudo pacman -S shntool mac                                                               # 分割cue
# shntool split -f example.cue -t %p\ %n\ %t -o flac example.ape 将example.ape分割 同时转成flac格式
yay -S zoom # 视频会议
```

<br>

编程
```shell
# python
sudo pacman -S jupyterlab python-sympy python-pillow
sudo pacman -S python-scikit-learn python-numba python-cvxopt python-matplotlib
sudo pacman -S python-tensorflow-opt-cuda python-pytorch-opt-cuda tensorboard
yay -S python-cvxpy python-pymc3

# octave
sudo pacman -S octave octave-general
sudo pacman -S octave-control octave-image octave-io octave-miscellaneous octave-signal octave-statistics

# julia
sudo pacman -S julia

# dot
sudo pacman -S graphviz xdot dot2tex

# 各种格式化工具
sudo pacman -S clang shfmt autopep8 yapf
```

<br>

压制
```shell
sudo pacman -S vapoursynth ffms2
yay -S vapoursynth-editor-git                        # 脚本编辑
yay -S vapoursynth-plugin-lsmashsource-git           # 分离mp4
yay -S d2vwitch-git vapoursynth-plugin-d2vsource-git # 分离ts
yay -S vapoursynth-plugin-yadifmod-git               # 反交错
yay -S vapoursynth-plugin-knlmeanscl-git             # 降噪
yay -S vapoursynth-plugin-havsfunc-git               # 函数合集
yay -S vapoursynth-plugin-damb-git                   # 音频处理
yay -S vapoursynth-plugin-delogo-git                 # 去台标
yay -S vapoursynth-plugin-waifu2x-w2xc-git           # 放大
```

<br>

字体
```shell
pacman -S otf-fira-code mathjax
yay -S otf-eb-garamond otf-font-awesome-4
```

<br>

picom 和 conky
```shell
yay -S picom-tryone-git # 透明工具
yay -S conky-cairo      # conky
```

<br>

最后有如下画面：
![conky](https://raw.githubusercontent.com/murongxixi/Arch/master/img/with-conky.png)
