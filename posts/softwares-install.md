　　重启进入系统后，还有大量的软件要安装

```shell
chsh -s /bin/zsh # 更换shell为zsh
```

```shell
yay -S otf-eb-garamond otf-font-awesome-4 
```

```shell
# 显卡驱动
pacman -S intel-ucode intel-media-driver intel-compute-runtime
pacman -S libva-vdpau-driver mesa-vdpau libvdpau-va-gl vdpauinfo
pacman -S nvidia-dkms nvidia-utils opencl-nvidia
pacman -S bumblebee primus
systemctl enable bumblebeed.service
usermod -aG bumblebee avanti
```

```shell
# 常用工具
pacman -S betterlockscreen                                # 锁屏
betterlockscreen -u /home/avanti/.config/awesome/themes/avanti/wallpaper.jpg
pacman -S curl wget youtube-dl transmission-qt ffsend-bin # 下载
pacman -S v2ray qv2ray-dev-git qv2ray-plugin-ssr-dev-git  # 科学上网
pacman -S proxychains-ng                                  # 科学上网
pacman -S geogebra                                        # 画图
pacman -S flameshot deepin-screenshot                     # 截图
pacman -S deepin-image-viewer                             # 看图
pacman -S deepin-picker                                   # 取色
pacman -S baidupcs-go                                     # 度盘
yay -S baidunetdisk-bin                                   # 度盘
yay -S xdman                                              # 下载
yay -S conky-cairo                                        # conky
yay -S ttf-font-awesome-4 otf-font-awesome-4              # 符号字体
yay -S wechat-uos                                         # 微信
yay -S com.tencent.meeting.deepin                         # 腾讯会议
```

```shell
# wps
yay -S wps-office-cn wps-office-mime-cn wps-office-mui-zh-cn ttf-wps-fonts

# latex 如果不在乎节省硬盘空间可以直接 pacman -S texlive-most texlive-lang
pacman -S texlive-core texlive-latexextra texlive-science texlive-pictures texlive-fontsextra texlive-langchinese

# latexindent依赖的perl库
pacman -S perl-log-log4perl perl-yaml-tiny perl-file-homedir perl-unicode-linebreak
yay -S perl-log-dispatch

# pdf
pacman -S evince okular pdftk
yay -S masterpdfeditor-free

# markdown编辑 markdown词典
pacman -S typora pandoc pandoc-citeproc pandoc-crossref princexml
pacman -S hunspell hunspell-en_AU hunspell-en_CA hunspell-en_GB hunspell-en_US

# 博客
pacman --noconfirm -S npm hugo
npm install -g gitbook-cli # aur里的包都过期了
```

```shell
# 影音娱乐
pacman -S ghostscript inkscape imagemagick krita                                    # 图片
pacman -S mpv mpv-bash-completion-git vlc gpac mediainfo-gui mkvtoolnix-gui aegisub # 视频
pacman -S audacious mpg123 spek-git                                                 # 音乐
pacman -S shntool mac                                                               # 分割cue
# shntool split -f example.cue -t %p\ %n\ %t -o flac example.ape 将example.ape分割 同时转成flac格式
```

```shell
# python
pacman -S jupyterlab python-sympy python-pillow
pacman -S python-scikit-learn python-cvxopt python-matplotlib python-seaborn
pacman -S python-tensorflow-opt-cuda python-pytorch-opt-cuda tensorboard
yay -S python-cvxpy python-pymc3

# octave
pacman -S octave octave-general
pacman -S octave-control octave-image octave-io octave-miscellaneous octave-signal octave-statistics

# julia
pacman -S julia

# css
pacman -S ruby-sass

# dot
pacman -S graphviz xdot dot2tex

# 各种格式化工具
pacman -S clang shfmt autopep8 yapf
```

```shell
# 视频压制
pacman -S vapoursynth vapoursynth-plugin-lsmashsource vapoursynth-plugin-mvtools vapoursynth-plugin-deblock vapoursynth-plugin-fluxsmooth ffms2

yay -S vapoursynth-editor                            # 脚本编辑
yay -S d2vwitch-git vapoursynth-plugin-d2vsource-git # 分离ts
yay -S vapoursynth-plugin-yadifmod-git               # 反交错
yay -S vapoursynth-plugin-knlmeanscl-git             # 降噪
yay -S vapoursynth-plugin-havsfunc-git               # 函数合集
yay -S vapoursynth-plugin-damb-git                   # 音频处理
yay -S vapoursynth-plugin-delogo-git                 # 去台标
yay -S vapoursynth-plugin-waifu2x-w2xc-git           # 放大
```

　　全部配置好有如下界面：
![安装完成](https://avanti1980.gitee.io/archlinux/img/success-install.png)