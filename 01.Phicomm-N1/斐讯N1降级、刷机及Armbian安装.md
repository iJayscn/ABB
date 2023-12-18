最前
最近捡垃圾不断，之前购买了斐讯遗产 K2P（真香，最近刷了 OpenWrt）、T1（刷了电视盒子）和蜗牛星际（刷了黑群晖），最近又捡了台斐讯 N1，主要用于做一些简单的爬虫和 Adguard Home 服务。其实原先有过一台树莓派 3B，但是 emmmm 吃灰了一年，然后二手卖了。虽然近期树莓派 4 发布了，性能有较大提升，但由于价格的原因，还是 N1 香。刷机过程由于已有的几篇文章有些内容已过时，或者记录不清楚，特记录如下，供大家参考。

〇、准备
USB 连接线（双公头）
HDMI 连接线
U 盘
USB 鼠标（有线或 USB 无线）
降级软件包（adb 工具、降级固件）：链接：https://share.weiyun.com/5HAZqG2 密码：tejb5k
官改软件包（webpad 官改系统、USB Burning Tool）：链接：https://share.weiyun.com/5HAZqG2 密码：bbj7ch
刷机软件包（我使用的 Armbian 5.91 系统、Armbian 5.44 系统、win32diskimager）：链接：https://share.weiyun.com/58HhQXG 密码：r8h5a9
Armbian 镜像：https://yadi.sk/d/pHxaRAs-tZiei
一、降级
用 HDMI 连接线显示器，通电检查系统版本号。由于斐讯官方固件比较新，如果版本号大于 V2.22，则需要先进行降级操作，否则无法刷机 $^1$。如果版本号小于 V2.22，则可以直接刷机。

插入 USB 鼠标，建议插在距离 HDMI 较近的 USB 接口上；连入网络，有线无线均可。屏幕上会显示鼠标、版本号和 IP 地址。

鼠标连续点击版本号四次，提示打开 adb。

电脑上利用 cmd，连接 adb，需要先将 adb 加入环境变量或在 adb 工具目录下运行，建议选择后者，并将降级文件放在同一文件夹中。

 adb 连接盒子
adb connect 盒子的 IP 地址
 进入 fastboot
adb shell reboot fastboot
 刷入 bootloader
fastboot flash bootloader bootloader.img
 刷入 boot
fastboot flash boot boot.img 
 刷入 recovery
fastboot flash recovery recovery.img
 重启 fastboot
fastboot reboot
重启盒子进入系统，屏幕上的版本号不会发生变化，但已降级成功，可进一步开始刷机。如果不确定，可以使用如下命令查看 fastboot 设备。

fastboot devices -l
注：如果觉得手动刷机比较麻烦，也可以使用自动化工具，可在 ZNDS 等论坛找到。

二、刷机（电视盒子）
刷入 webpad 官改系统，先使用 USB Burning Tool 工具加载 N1 的 wepad 官方系统镜像或其它想刷入的安卓镜像，导入后会进行完整性检查，时间根据电脑性能和镜像大小改变。



请注意取消擦除 flash和擦除 bootloader选项，然后点击开始按钮。

利用 USB 双头线连接盒子和电脑，建议插在距离 HDMI 较近的 USB 接口上，利用 adb 工具连接盒子。

 adb 连接盒子
adb connect 盒子的 IP 地址
 进入 fastboot
adb shell reboot update
烧录时间较长，完成后点击停止按钮，拔掉电源重新开机即可，系统会进入安卓系统，可以配合遥控器当电视盒子使用（相比于小米盒子等，性价比超高，真香）

三、刷机（Armbian）
由于我已经有了一台 T1 当电视盒子用，所以这台 N1 准备刷入 Armbian 当 Linux 用，也是我刷机过程中遇到坑最多的地方。

选择系统，这里使用 Armbian 系统作为案例。根据前人的刷机记录，Armbian 系统在 5.44 版本之后发生了较大变化 $^2$，也有说是在 5.62 版本发生了变化 $^3$，由于最新版本已经来到了 5.91，所以我也没有仔细核实具体是哪个版本发送了变化。详细的系统命名规则可参考：斐讯 N1 折腾记：运行 Linux 及优化

打开Win32DiskImager，选择需要刷入的img文件和U 盘，时间可能要几分钟。

写入完成后，在 Windows 系统下会有一个Boot盘符，需做如下修改：

如果选择 5.44 版本（5.62 版本）及之前的系统：复制dtb/meson-gxl-s905d-p230.dtb（4.16、4.18 内核）或gxl_p230_2g.dtb（其它老内核）到根目录，并重命名为dtb.img。

如果选择 5.44 版本（5.62 版本）之后的系统：打开根目录下的uEnv.ini文件，将dtb_name值改为/dtb/meson-gxl-s905d-phicomm-n1.dtb（4.18 内核）或/dtb/meson-gxl-s905d-p230.dtb（其它老内核），且确定/dtb目录下有该文件。如果不存在该文件，可在 https://github.com/yangxuan8282/phicomm-n1/releases 中找到下载。本人选用了 5.91 版本系统，在/dtb目录下已有该文件，因此只需修改配置文件即可。



将 U 盘插入靠近 HDMI 的 USB 接口，通电开机，一般会进入 Armbian 系统。如果仍然进入安卓系统，则可以使用 adb 命令重启 N1，进入recovery模式后点击 reboot to system 启动系统。

adb connect 盒子的 IP 地址
adb shell reboot update
Armbian 系统，可切换至清华镜像以提高安装包的速度。

注：该系统的系统负载显示可能存在错误，一直在 2 以上，但是查看 CPU 和内存占用，基本没有什么消耗，应该是系统显示的问题，可忽略。

四、参考文献
[1] 斐讯 n1 安装 linux 系统

[2] 斐讯 N1 折腾记：运行 Linux 及优化

[3] 斐讯 N1 刷 Armbian Linux 做服务器

https://www.busby.com.cn/2019/07/23/%E6%96%90%E8%AE%AFN1%E9%99%8D%E7%BA%A7%E3%80%81%E5%88%B7%E6%9C%BA%E5%8F%8AArmbian%E5%AE%89%E8%A3%85/
