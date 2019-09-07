# linux-chuwi_Vi10
 
让ubuntu 在 驰为 chuwiVi10 上运行

[!效果图](./pic/chuwi-Vi10.png)

windows、安卓二合一平板：驰为Vi10
###  平板介绍
- CPU/GPU
处理器架构X86架构
处理器技术英特尔芯平板
处理器型号Intel Atom Z3736F
处理器主频1.33GHz
最高频率2.16GHz
处理器核心四核心
显卡芯片Intel HD Graphic
- 存储设备
系统内存2GB
内存技术LPDDR3
存储容量32GB
存储介质eMMC
存储扩展支持Micro SD（TF）卡，最大支持128GB
- 显示屏
屏幕尺寸10.6英寸
屏幕分辨率1366x768
屏幕像素密度148PPI
屏幕描述电容式触摸屏，多点式触摸屏
指取设备触摸屏
支持语言支持多国语言

### 探索过程
	之前装了android和win10双系统，突然，某一天，触摸屏用不了，emmm
android系统好像就用途不大了，win10系统又太耗电了，于是乎打算刷linux系统。
	网上搜驰为Vi10刷linux的帖子，没有啥收获，但是看到了其他平板安装linux系统的例子，在参考文章中会一一列出。参考网上的文章进行系统的安装，在qq群里询问哪个发行版的linux好用，尝试了debian，lubuntu，ubuntu，系统的安装，三个系统的安装情况:
	debian：进入安装界面，跟着引导区安装系统，引导安装包安装失败，没有试用系统界面，无法联网；
	lubuntu：进入安装界面，部分图标不能显示，点击安装图标无反应，放弃；
	ubuntu：前前后后装了五六次，不是未知错误就是grub-efi-ia32 引导安装失败，这个错误有解决办法。有试用系统界面，虽然wifi用不了，但是可以使用手机的USB网络共享去给平板电脑提供网络。csdn博客上说grub-efi-ia32 引导装不上的时候可以通过联网，系统自动去下载相关软件进行安装，终于，装好了！
	将ubuntu-gnome-16.04 LST amd64 装到平板上了！
	装系统期间发现wifi不能正常使用，于是乎去网上搜索，最后在qq群内的网友的帮助下成功驱动了wifi，在此感谢“考古专家”。

## 1. 制作启动盘
1. 烧写镜像
软件：[rufus](http://rufus.akeo.ie/?locale=zh_CN)
镜像：[ubuntu-gnome-16.04.5-desktop-amd64.iso（从国内镜像站获取）](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cdimage/ubuntu-gnome/releases/16.04.5/release/ubuntu-gnome-16.04.5-desktop-amd64.iso)

使用软件 rufus 将ISO镜像文件烧写到U盘（大于4G），并且将U盘更名为WINPE（方便平板电脑识别到）
2. 加入引导文件
引导文件：[bootia32.efi](https://cdn.wyr.me/wp-content/uploads/2015/12/bootia32.efi_.zip)

下载bootia32.efi文件并解压复制到'EFI\BOOT'目录下。


**注意事项**
镜像的话，要使用64位的镜像，但是由于平板的BIOS不支持64位的EFI引导，所以需要用到一个32位的EFI引导程序（此部分在参考文章2内有详细讲解）bootia32.efi。

## 2. 安装操作系统
将启动盘插入平板电脑，平板电脑开机后按 ESC 键，进入BISO界面，在 SCU 那里关掉 fast boot 和 quiet boot。
选择U盘启动。 
手机开启USB网络共享，接到平板电脑的USB口，给平板电脑提供网络，为引导安装提供网络支持。

进入到ubuntu使用界面，确保手机连接平板电脑之后，平板能联网（在文件管理器中右键打开，ping一下试验下有没网）。
安装系统，分区，选择手动分区，我的分区情况：
EFI分区 200MB
/  Ext4文件 29G
swap 交换空间 1G

选择引导安装到 Ext4文件的那个分区。

安装系统过程......

过程中如果没有网络的话，会报错：“GRUB安装失败，无法将 grub-efi-ia32软件包安装到/target/中”，由于镜像中不存在32位引导，所以在断网的情况下是会报错的，联网之后系统会自动去下载缺少的文件。





## 过程中遇到的问题
### 系统安装过程
当系统提示error input 啥的时候，去winPE那里重新格式化平板的分区，重新安装系统。



### WiFi
一开始的时候wifi不能正常使用，扫描不到wifi信号。

输入命令
```bash
dmesg
```
可以看到相关报错信息。
[!wifi_error](./pic/wifi_error.jpg)

加载固件失败了。
```bash
Direct firmware load for rtlwifi/rtl8723bs_nic.bin failed with error -2
```
需要下载固件：
去[这里](https://github.com/hadess/rtl8723bs/tree/test_locking) 或者[这里](https://github.com/RainMark/egg_wifi/tree/master/r8723bs)下载整个文件夹，将firmware内的 bin文件 新建一个名为rtlwifi的文件夹，放进去，然后将文件夹拷贝到平板的 /lib/firmware/下，重启电脑即可。

固件文件也可以在 此网页的 ./firmware/rtlwifi.tar.xz 中找到

### 音频
dmesg报错
```bash
Audio Port: ASoC: no backend DAIs enabled for Audio Port
```

尚未解决。


### 参考文章：
1. [华硕TRANSFORMER BOOK T100上的UBUNTU（或其他LINUX）](http://www.jfwhome.com/2014/03/07/perfect-ubuntu-or-other-linux-on-the-asus-transformer-book-t100/)
2. [台电平板-Windows平板加装Ubuntu实现双系统的尝试，有图有真相](http://bbs.teclast.com/thread-254487-1-1.html)
3. [iwork8平板电脑安装ubuntu，Z3735d/f系列CPU通用](https://www.cnblogs.com/wjyHIT2011/articles/6824442.html)


