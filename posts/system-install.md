　　archlinux的安装非常反人类，记录一下以便日后查用

　　制作U盘引导镜像 /dev/sd*是U盘的盘符
```shell
dd if=archlinux-2019.08.01-x86_64.iso of=/dev/sd*
```

　　进入引导镜像后，先用wifi-menu连接无线网络，如果是有线直接就可以开始了
```shell
timedatectl set-ntp true # 刷新本地时间以确保时间准确无误

nano /etc/pacman.d/mirrorlist # 修改源列表 保留下面几个就行了
# Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
# Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
# Server = http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
# Server = http://mirrors.163.com/archlinux/$repo/os/$arch
# Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch
# Server = http://mirrors.neusoft.edu.cn/archlinux/$repo/os/$arch

# 格式化 
mkfs.ext4 /dev/nvme0n1p4
mkfs.ext4 /dev/nvme0n1p5

# 挂载 创建目录
mount /dev/nvme0n1p4 /mnt
mkdir /mnt/home
mount /dev/nvme0n1p5 /mnt/home
mkdir /mnt/efi
mount /dev/nvme0n1p1 /mnt/efi

pacstrap /mnt base base-devel     # 安装基础包
genfstab -U /mnt >>/mnt/etc/fstab # 生成fstab文件
arch-chroot /mnt                  # 进入安装好的系统
```

　　处理pacman源
```shell
# 反注释掉Color VerbosePkgLists TotalDownload
sed -i 's/#Color/Color/g' /etc/pacman.conf
sed -i 's/#VerbosePkgLists/VerbosePkgLists/g' /etc/pacman.conf

# 反注释掉[multilib]
no=$(cat /etc/pacman.conf | grep -n "\[multilib\]" | cut -d ':' -f 1)
sed -i "${no},+1 s/#//g" /etc/pacman.conf

# 添加非官方源archlinuxcn
if [ -z "$(cat /etc/pacman.conf | grep archlinuxcn)" ]; then
    echo -e "\n[archlinuxcn]" >>/etc/pacman.conf
    echo Server = http://mirrors.ustc.edu.cn/archlinuxcn/\$arch >>/etc/pacman.conf
fi

# 添加非官方源arch4edu
if [ -z "$(cat /etc/pacman.conf | grep arch4edu)" ]; then
    echo -e "\n[arch4edu]" >>/etc/pacman.conf
    echo SigLevel = Never >>/etc/pacman.conf
    echo Server = http://mirrors.tuna.tsinghua.edu.cn/arch4edu/\$arch >>/etc/pacman.conf
fi

# update
rm -fr /etc/pacman.d/gnupg
pacman-key --init
pacman-key --populate archlinux
pacman -Sy archlinuxcn-keyring
```

　　内核、驱动、引导和必要的设置
```shell
pacman -S linux-zen linux-zen-headers linux-firmware sof-firmware aic94xx-firmware wd719x-firmware

ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime # 设置时区
hwclock --systohc                                       # 硬件时间设置 默认为UTC时间

# 本地化
sed -i 's/#en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/g' /etc/locale.gen
sed -i 's/#zh_CN.UTF-8 UTF-8/zh_CN.UTF-8 UTF-8/g' /etc/locale.gen
locale-gen                              # 生成本地化文件/etc/locale.gen
echo LANG=zh_CN.UTF-8 >/etc/locale.conf # 设置系统默认语言为简体中文

echo avanti-xps17 >/etc/hostname # 设置主机名
echo 127.0.0.1 localhost >/etc/hosts
echo ::1 localhost >>/etc/hosts

# 安装引导
pacman -S grub os-prober efibootmgr

# 在/efi/EFI分区下创建GRUB/grubx64.efi文件
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB

# 开启os-prober 现在grub默认将其关闭
echo -e "\nGRUB_DISABLE_OS_PROBER=false" >>/etc/default/grub

# xps17需要修改启动文件启动wifi
sed -i 's/loglevel=3 quiet/loglevel=3 memmap=12M\\$20M quiet/g' /boot/grub/grub.cfg 

# 生成配置文件 同时自动找回win10的启动文件
grub-mkconfig -o /boot/grub/grub.cfg
```

　　账户
```shell
passwd # 设置root密码
sed -i 's/# %wheel ALL=(ALL) ALL/%wheel ALL=(ALL) ALL/g' /etc/sudoers
useradd -m -g wheel avanti # wheel组可以通过sudo获取root权限
passwd avanti              # 设置普通账户的密码
```

　　常用软件
```shell
pacman -S awesome # 我喜欢平铺的awesome

pacman -S xorg xorg-drivers
pacman -S lightdm lightdm-slick-greeter # 用lightdm登录
systemctl enable lightdm                # 设置开机自启

pacman -S nano mousepad

pacman -S yay pacman-contrib git downgrade

pacman -S networkmanager dnsmasq # 网络管理器 创建热点
systemctl enable NetworkManager

pacman -S openssh sshpass # ssh
systemctl enable sshd

pacman -S fastfetch-git htop sysstat # 查看系统信息

pacman -S alsa-utils pulseaudio pulseaudio-alsa # 声音

pacman -S picom-git # 透明工具

# 终端模拟器 浏览器 文本编辑器 文件管理器 软件启动器
pacman -S rxvt-unicode google-chrome visual-studio-code-bin pcmanfm rofi

# 输入法
pacman -S fcitx5-git fcitx5-gtk-git fcitx5-qt5-git fcitx5-chinese-addons-git fcitx5-mozc-git fcitx5-configtool

# zsh
pacman -S zsh autojump thefuck oh-my-zsh-git zsh-completions zsh-autosuggestions zsh-syntax-highlighting zsh-theme-powerlevel10k powerline-common powerline-fonts

# 创建文件目录 图标主题 自动挂载移动硬盘 处理访问权限 添加回收站
pacman -S xdg-user-dirs arc-icon-theme mate-polkit udiskie gvfs

# 读写ntfs磁盘 文件压缩 查找文件 挂载远程服务器磁盘到本地
pacman -S ntfs-3g unrar p7zip file-roller mlocate sshfs
# 用法 sudo sshfs 用户名@ip地址:远程目录 本地目录

# 惠普打印机
pacman -S cups hplip hplip-plugin system-config-printer
systemctl enable cups

# 字体
pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
pacman -S wqy-bitmapfont wqy-microhei wqy-microhei-lite wqy-zenhei
pacman -S ttf-merriweather ttf-merriweather-sans
pacman -S ttf-fira-code woff-fira-code woff2-fira-code
pacman -S mathjax awesome-terminal-fonts nerd-fonts-complete
fc-cache -fv
```

　　下载awesomewm配置文件
```shell
git config --global user.name "murongxixi"
git config --global user.email "murongxixi@gmail.com"
git clone https://gitee.com/avanti1980/awesomewm.git # 下载配置文件
mv awesomewm /home/avanti/.config/awesome
```

　　配置lightdm
```shell
mkdir -p /usr/share/backgrounds

cp /home/avanti/.config/awesome/themes/avanti/wallpaper.jpg /usr/share/backgrounds/wallpaper.jpg
chmod +644 /usr/share/backgrounds/wallpaper.jpg

sed -i 's/greeter-session=\(.*\)/greeter-session=lightdm-slick-greeter/g' /etc/lightdm/lightdm.conf

echo \[Greeter\] >/etc/lightdm/slick-greeter.conf
echo background=/usr/share/backgrounds/wallpaper.jpg >>/etc/lightdm/slick-greeter.conf
echo draw-grid=false >>/etc/lightdm/slick-greeter.conf
echo enable-hidpi=on >>/etc/lightdm/slick-greeter.conf
echo xft-dpi=192.0 >>/etc/lightdm/slick-greeter.conf
```

　　自动挂载Windows分区
```shell
mkdir -p /home/avanti/Windows/System /home/avanti/Windows/Data

uuid_array=$(echo $(lsblk -f | grep -n "ntfs" | sed 's/[ ][ ]*/ /g' | cut -d ' ' -f 3))
num=$(echo ${uuid_array} | wc -w)
((num--))
uuid_array=(${uuid_array})

echo -e "UUID=${uuid_array[0]} /home/avanti/Windows/System ntfs-3g defaults 0 0\n" >>/etc/fstab
echo -e "UUID=${uuid_array[$num]} /home/avanti/Windows/Data ntfs-3g defaults 0 0\n" >>/etc/fstab
```
