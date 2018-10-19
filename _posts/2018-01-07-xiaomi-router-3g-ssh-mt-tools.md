---
layout: post
title: "小米路由器 3G 开启SSH 安装 MT 工具箱"
tagline: ""
description: ""
category: 经验总结
tags: [router, xiaomi,]
last_updated: 
---

下面是小米路由器折腾记录，包括开启 SSH，然后安装 MT 工具箱，主要是为了其中的两个插件，一个是去广告，一个是SS代理，不过附带竟然发现了 frp 插件，开心啊。下面就是具体的记录。

## 小米路由器刷入开发版
[下载](http://www1.miwifi.com/miwifi_download.html) [ROM for R3G 开发版](http://bigota.miwifi.com/xiaoqiang/rom/r3g/miwifi_r3g_firmware_c2175_2.25.122.bin)，在后台点击上传安装开发版的bin，然后等待重启，完成开发版安装。

## 小米路由器开启 SSH
小米帐号绑定小米路由器，设置路由器可正常上网，并使用手机版小米wifi绑定路由器，然后在绑定小米账号的前提下，进入 <https://d.miwifi.com/rom/ssh> 这个网站，下载对应路由器的工具包`miwifi_ssh.bin`（即使同一个型号的多个路由器，工具包文件内容也不一样）。然后找到 SSH 登录的 root 密码，之后会用到。

> 工具包使用方法：小米路由器需升级到开发版0.5.28及以上，小米路由器mini需升级到开发版0.3.84及以上，小米路由器3即将支持。注意：稳定版不支持。
> 请将下载的工具包bin文件复制到U盘（FAT/FAT32格式）的根目录下，保证文件名为miwifi_ssh.bin；
> 断开小米路由器的电源，将U盘插入USB接口；
> 按住reset按钮之后重新接入电源，指示灯变为黄色闪烁状态即可松开reset键；
> 等待3-5秒后安装完成之后，小米路由器会自动重启，之后您就可以尽情折腾啦 ：）

如果Chrome浏览器出现错误提示："This site can’t be reached. d.miwifi.com refused to connect. ERR_CONNECTION_REFUSED"，需要手动将`http`替换为`https`。

重启完成之后可以使用 `ssh root@192.168.31.1` 来连接路由器，使用之前获取的 SSH root 密码登录。

下面从 Monlor 和 Misstar Tools 两者中选择一个工具箱来完成 Shadowsocks 的安装。

## 输入 Monlor 工具箱
[Monlor](https://github.com/monlor/Monlor-Tools) 是我自用的工具箱，亲测可用。

SSH 登录之后执行如下代码，安装 Monlor 工具箱：

    sh -c "$(curl -kfsSl https://coding.net/u/monlor/p/Monlor-Tools/git/raw/master/install.sh)" && source /etc/profile &> /dev/null

初始化：

    init.sh 

然后执行`monlor`，进入命令行交互界面进行 Shadowsocks 服务的安装和配置。配置完成之后可以在路由器管理界面控制 Shadowsocks 服务。

## 刷入 MT 工具箱
MT工具箱是目前第三方插件里面最为方便易用的插件集合

KMS 服务器，VSFTP 服务器，VPN 服务器，远程管理，ARIA2，Koolproxy广告过滤，阿呆喵广告过滤，Shadowsocks，webshell， frp 服务

SSH 登录之后执行如下代码，安装 Misstar Tools 2.0工具箱：

    wget http://www.misstar.com/tools/appstore/install.sh -O /tmp/install.sh && chmod a+x /tmp/install.sh && /tmp/install.sh 

卸载：

    sh -x etc/misstar/scripts/uninstall.sh

安装 Misstar Tools 之后，有两种方法可以安装 Shadowsocks 插件，第一种就是使用备份的文件，传入路由器之后运行安装，第二种直接在安装页面修改页面内容，推荐使用第二种方法。

### 方法一：使用文件安装

1. 将压缩包中两个文件传到路由器，放在同一目录，名字不要改。
2. 执行：`chmod +x ./install_ss & ./install_ss add`
3. 刷新路由器后台，科学上网插件已经出现了。
4. 卸载的时候执行：`chmod +x ./install_ss & ./install_ss del`

from: <http://bbs.xiaomi.cn/t-13765387>

### 方法二：修改页面ID

使用 Chrome 打开 MT 插件管理页面，使用开发者工具，定位页面中任意一个 安装 按钮，然后找到代码中的 `id="ftp"` 字样，修改为 `id="ss"` ，然后点安装，成功后会回到 MT 工具箱首页，配置使用即可。

## 刷其他固件
按照上面官方的步骤操作完路由器就已经获取root权限了，再使用ssh工具连接路由即可，建议在进行下一步操作之前备份原版分区文件

    root@XiaoQiang:~# cat /proc/mtd
    dev:    size   erasesize  name
    mtd0: 07f80000 00020000 "ALL"
    mtd1: 00080000 00020000 "Bootloader"
    mtd2: 00040000 00020000 "Config"
    mtd3: 00040000 00020000 "Bdata"
    mtd4: 00040000 00020000 "Factory"
    mtd5: 00040000 00020000 "crash"
    mtd6: 00040000 00020000 "crash_syslog"
    mtd7: 00040000 00020000 "reserved0"
    mtd8: 00400000 00020000 "kernel0"
    mtd9: 00400000 00020000 "kernel1"
    mtd10: 02000000 00020000 "rootfs0"
    mtd11: 02000000 00020000 "rootfs1"
    mtd12: 03580000 00020000 "overlay"
    mtd13: 012a6000 0001f000 "ubi_rootfs"
    mtd14: 030ec000 0001f000 "data"

查看U盘挂载的位置命令：

    df -h

U盘一般是/extdisks/开头，后面的可能不一样，我的是：/extdisks/sda4/

备份小米路由器3G原版分区到文件，每行是一条命令，分别执行，最后一条可能会报错，可不用理会，最有用的是mtd0-mtd4

    dd if=/dev/mtd0 of=/extdisks/sda4/rom/ALL.bin
    dd if=/dev/mtd1 of=/extdisks/sda4/rom/Bootloader.bin
    dd if=/dev/mtd2 of=/extdisks/sda4/rom/Config.bin
    dd if=/dev/mtd3 of=/extdisks/sda4/rom/Bdata.bin
    dd if=/dev/mtd4 of=/extdisks/sda4/rom/Factory.bin
    dd if=/dev/mtd5 of=/extdisks/sda4/rom/crash.bin
    dd if=/dev/mtd6 of=/extdisks/sda4/rom/crash_syslog.bin
    dd if=/dev/mtd7 of=/extdisks/sda4/rom/reserved0.bin
    dd if=/dev/mtd8 of=/extdisks/sda4/rom/kernel0.bin
    dd if=/dev/mtd9 of=/extdisks/sda4/rom/kernel1.bin
    dd if=/dev/mtd10 of=/extdisks/sda4/rom/rootfs0.bin
    dd if=/dev/mtd11 of=/extdisks/sda4/rom/rootfs1.bin
    dd if=/dev/mtd12 of=/extdisks/sda4/rom/overlay.bin
    dd if=/dev/mtd13 of=/extdisks/sda4/rom/ubi_rootfs.bin
    dd if=/dev/mtd14 of=/extdisks/sda4/rom/data.bin


首先，下载 Breed 刷入不死 breed，

下载地址：<https://breed.hackpascal.net/>  (搜索 breed-mt7621-xiaomi-r3g.bin)

用命令下载，在电脑终端中运行

    wget https://breed.hackpascal.net/breed-mt7621-xiaomi-r3g.bin

将文件传入路由器

    scp breed-mt7621-xiaomi-r3g.bin root@192.168.31.1:/tmp/

进入路由器

    ssh root@192.168.31.1
    cd /tmp
    mtd -r write breed-mt7621-xiaomi-r3g.bin Bootloader

机器会重新启动，指示灯变蓝，确保电脑设置为自动获取 IP 地址，使用网线连接。刷入成功后，断掉电源，用东西顶住复位键不松开，然后再接上电源等待10秒左右放开复位键，浏览器输入 http://192.168.1.1  即可进行Breed Web恢复界面：

如果要刷入其他固件，打开Breed Web恢复控制台，点击左侧“固件更新”，钩选“固件”，选择固件，再点“上传”

更多内容可查看： <http://www.right.com.cn/forum/thread-161906-1-1.html>


## reference

- <http://www.miui.com/thread-7520321-1-2.html>
- <https://blog.digua.co/post/105/mi-r3g-chinatelecom-padavan>
- <http://tw.miui.com/thread-31394-1-1.html>
- <http://zh.roomchat.im/index.php?title=Category:%E5%BC%80%E6%BA%90%E8%BD%AF%E4%BB%B6/Monlor>
