　　查看设备情况

```shell
┌─[murongxixi@murongxixi-xps] - [~/Gitbook/Arch] - [五 8月 09, 23:27]
└─[$] <git:(master*)> nmcli device
DEVICE           TYPE      STATE   CONNECTION 
enp0s20f0u2u3    ethernet  已连接   有线连接 1   
wlp59s0          wifi      已断开   --         # 无线暂时没有连接
p2p-dev-wlp59s0  wifi-p2p  已断开   --         
lo               loopback  未托管   --
```

<br>

　　查看可以搜到的无线信号

```shell
┌─[murongxixi@murongxixi-xps] - [~/Gitbook/Arch] - [五 8月 09, 23:27]
└─[$] <git:(master*)> nmcli device wifi list
IN-USE  SSID          MODE   CHAN  RATE        SIGNAL  BARS  SECURITY  
        28-2-902      Infra  10    130 Mbit/s  79      ▂▄▆_  WPA1 WPA2 
        CMCC-Q2aM     Infra  2     130 Mbit/s  75      ▂▄▆_  WPA1 WPA2 
        TP-LINK_51D0  Infra  11    405 Mbit/s  52      ▂▄__  WPA1 WPA2 
        28-2-902_5G   Infra  36    135 Mbit/s  52      ▂▄__  WPA1 WPA2 
        TP-LINK_702   Infra  6     405 Mbit/s  50      ▂▄__  WPA1 WPA2
```

<br>

　　连接名称为CMCC-Q2aM、密码为123456的无线网络

```shell
┌─[murongxixi@murongxixi-xps] - [~/Gitbook/Arch] - [五 8月 09, 23:29]
└─[$] <git:(master*)> nmcli device wifi connect CMCC-Q2aM password 123456
成功用 "wlp59s0c192bc0c-64fb-484d-91d0-a32ebfacb1df" 激活了设备 ""。
```

若成功连上，在/etc/NetworkManager/system-connections目录下会生成CMCC-Q2aM.nmconnection文件，下次要想再连接该网络直接`nmcli connection up CMCC-Q2aM`即可。

<br>

　　查看当前保存的全部连接
```shell
┌─[murongxixi@murongxixi-xps] - [~/Gitbook/Arch] - [六 8月 10, 00:13]
└─[$] <git:(master*)> nmcli connection show                              
NAME           UUID                                  TYPE      DEVICE        
有线连接 1      c87e77aa-76fb-34db-8203-372d6218ab26  ethernet  enp0s20f0u2u3 
CMCC-Q2aM      c192bc0c-64fb-484d-91d0-a32ebfacb1df  wifi      wlp59s0       
木兰天池        b1871b1a-fb60-43bb-a9dd-b1947b217349  wifi      --            
小米手机        68a8f926-d160-4ba0-ae0b-4414dcdd35af  wifi      --            
HOJO qunsheng  ea4f578a-7a24-4587-9d1b-3c45b2e80f0b  wifi      --            
HUST_WIRELESS  98714e86-c3ec-4344-af42-a5f1ea28b119  wifi      --            
KST_WIFI       7f0310d3-6189-4b6d-89ed-f80405eddad6  wifi      --
```

<br>

　　断开当前的无线连接

```shell
┌─[murongxixi@murongxixi-xps] - [~/Gitbook/Arch] - [六 8月 10, 00:14]
└─[$] <git:(master*)> nmcli device disconnect wlp59s0
成功断开设备 "wlp59s0"。
```

<br>

　　创建名称为murongxixi、密码为××××××××××的热点

```shell
┌─[murongxixi@murongxixi-xps] - [~/Gitbook/Arch] - [六 8月 10, 00:59]
└─[$] <git:(master*)> nmcli device wifi hotspot ssid murongxixi password ××××××××××
成功用 "wlp59s005e6dbf3-724d-4e00-9756-ce1f6e00fae8" 激活了设备 ""。
```

若成功创建，在/etc/NetworkManager/system-connections目录下会生成Hotspot.nmconnection文件，下次要想再创建热点直接`nmcli connection up Hotspot`即可。
