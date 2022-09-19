

# XPS 13-9360 Monterey

| 类别                | 详细信息                                                     |
| ------------------- | ------------------------------------------------------------ |
| 电脑型号            | DELL XPS 13-9360                                             |
| Clover:当前系统版本 | macOS Mojave 10.14.6(18G84) & 10.15(Beta 3)                  |
| OC:当前系统版本     | macOS Monterey 12.0.1 (21A559)                               |
| BIOS                | 2.13.0 (2.6.2之后的版本有bug内存频率变成1867 MHz,但是不影响安装) |
| 处理器              | Intel Core i7-7560U/i7-8550U                                 |
| 内存                | 16 GB(DDR3L 2133 MHz)                                        |
| 硬盘                | SAMSUNG PM961 (512GB)                                        |
| 显卡                | Intel Iris Plus Graphics 640/UHD 620                         |
| 显示器              | QHD(Sharp触屏 3200x1800)                                     |
| 声卡                | ALC256 (ALC3246)                                             |
| 网卡                | 更换为 BCM94360cs2 （原网卡Killer 1535，也可以更换为DW1560/1830/1820A,否则无法驱动网卡，蓝牙也有些问题) |

![](https://hoan-pic.oss-cn-hangzhou.aliyuncs.com/img/info.png)

## 系统安装后注意事项

### 耳机

第一种：ALCPlugFix文件(来自:[daliansky黑果小兵](https://github.com/daliansky/dell7000)），双击ALCPlugFix/install.command即可，但是插上耳机无法使用耳麦，使用的电脑内置mic



第二种(推荐)：ComboJack(来自：[ohmygod1993](http://bbs.pcbeta.com/viewthread-1799183-1-1.html),[hackintosh-stuff](https://github.com/hackintosh-stuff/ComboJack)，修改xps9560上的驱动

1. 前提，使用过ALCPlugFix方式，先卸载，双击ALCPlugFix/uninstall.command即可，并删除CLOVER⁩ ▸ ⁨kexts⁩ ▸ ⁨Other⁩下的CodecCommander.kext驱动

2. 将kexts⁩ ▸ ⁨ComboJack_Installer⁩下的VerbStub.kext放到CLOVER⁩ ▸ ⁨kexts⁩ ▸ ⁨Other⁩下

3. 终端下执行⁨kexts⁩ ▸ ⁨ComboJack_Installer⁩下的install.sh重启即可

4. 插入耳机选择Headset,这样耳机和耳麦都正常工作 



### 网卡

1. 无线频段不够的可以在config中的Boot参数Arguments中添加`brcmfx-country=#a`,重启即可

2. 最新版本的AirportBrcmFixup在DW1830下显示网卡为第三方，使用没有影响，可以使用1.1.6版本还原
3. 苹果原装网卡需要添加AirportBrcmFixup驱动，否则睡眠有时候会被唤醒



### 显示器

如果QHD分辨率设备，在开机第二阶段苹果logo变大，在config的Boot Graphics的UIScale中填入`2`，重启即可

FHD或者外接显示器需要开启HIDPI，可以参考[xzhih/one-key-hidpi](https://github.com/xzhih/one-key-hidpi)

校色文件：Displays/RXN49_LQ133Z1_01.icm的文件是QHD的屏幕校色文件（来自：[grawlinson](https://github.com/grawlinson/dell-xps-9360/tree/master/display)），复制到`/Users/<username>/Library/ColorSync/Profiles`或者`/Library/ColorSync/Profiles`下，然后显示器偏好设置的颜色选择



### 蓝牙

关于蓝牙问题，将蓝牙目录下BrcmFirmwareData.kext和BrcmPatchRAM2.kext驱动放入clover对应驱动目录或者将BrcmFirmwareRepo.kext和BrcmPatchRAM2.kext放入到系统L/E目录下并重建缓存，官方解释说放到系统种内存效率更高，目前没看出来差别，BT4LEContiunityFixup.kext是修复Handoff功能，我没有需求，没有添加，自行测试,10.15使用10.15文件夹驱动

已更换白果卡,大家自行解决,最新蓝牙下载地址：https://github.com/acidanthera/BrcmPatchRAM/releases



### WIFI

关于WIFI问题，如果WIFI无法驱动，添加WIFI目录下的驱动，DW1830不需要，DW1560可能需要



### CPU

有低频需求的，建议使用[stevezhengshiqi/one-key-cpufriend](https://github.com/stevezhengshiqi/one-key-cpufriend)的脚本根据自己需求定制，有多种选择

或者以下脚本

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/stevezhengshiqi/one-key-cpufriend/master/one-key-cpufriend_cn.sh)"
```



### USB

目前有2种驱动方式RM的usbinjectall+uiac和基于fbPatcher.app生成的USBPort，目前没看出来哪个方式更好，我更加倾向于第一种

第一种也是默认方式使用RM的usbinjectall方式，将kext/USB/usbinjectall的SSDT-UIAC.aml放到CLOVER⁩/ACPI⁩/⁨patched⁩下，USBInjectAll.kext放到CLOVER⁩/kexts/Other下，并删除USBPorts.kext,重启

第二种方式，目前默认的方式，有个弊端，就是修改smbios后导致所有sub失效，需要修改USBPorts.kext,我默认添加了一个kext/USB/usbport/mbp14,1/USBPorts.kext,这个支持smbios为mbp14,1，替换原来的就好了，如果想更改其他smbios，教程如下：

1. 右键USBPorts.kext显示包内容
2. 随便一个文本工具打开Contents的Info.plist，修改几个信息即可



### 摄像头

> ProductID_22155   	(0x568b)
>
> VendorID_3034      	(0x0bda Realtek Semiconductor Corp.)

10.15及以上系统可不添加驱动即可正常使用，但是摄像头硬件存在差异，有不同的设备id，可能部分设备无法使用，可以尝试添加以下驱动之一(kexts目录下)

[UVC2FaceTimeHD.kext](./kexts/UVC2FaceTimeHD.kext)

[FaceTimeHD.kext](./kexts/FaceTimeHD.kext)



### SD卡驱动

可尝试以下驱动，由于我没有相关设备，大家自行测试

[cholonam/Sinetek-rtsx ](https://github.com/cholonam/Sinetek-rtsx/releases)

### 重建缓存

```
# 重建缓存
sudo kextcache -i /

# 某些原因更改了L/E或S/L/E驱动
sudo chown -R root:wheel /System/Library/Extensions/
sudo chmod -R 755 /System/Library/Extensions/
sudo kmutil install --update-all
sudo kcditto
```

### 睡眠模式（更好的睡眠）

```
sudo pmset -a hibernatemode 0
sudo pmset -a autopoweroff 0
sudo pmset -a standby 0
sudo rm /private/var/vm/sleepimage
sudo touch /private/var/vm/sleepimage
sudo chflags uchg /private/var/vm/sleepimage
```



### 已知问题

- ~~sd读卡器无法使用~~(BIOS可以关闭，节省电量)
- ~~雷电口需要开启前或者睡眠唤醒前插上，冷启动才会生效（生效后热插拔正常）~~
- ~~耳机麦克风不工作（电脑麦克风正常，插耳机时使用电脑麦克风）~~
- ~~睡眠前耳机没有拔下，唤醒后耳机可能无声，重新插拔下即可~~(ComboJack可能也把这个修复了)
- ~~蓝牙长时间睡眠后唤醒可能不工作，可能需要重新睡眠唤醒或者重启~~（新版本usbports.kext蓝牙不内建，同时关闭蓝牙高级选项，大幅改善问题，出问题几率很小）
- ~~Intel Iris Plus Graphics 640睡眠唤醒后有几率黑屏~~，WhateverGreen的framebuffer-flags补丁fixed



### BIOS推荐设置

- Sata: AHCI
- Disable Secure Boot
- Enable VT
- Disable VT-D
- Disable SD card reader (可选，关闭节省0.5w功耗)

---------

#### 安装注意事项（非常重要，否则可能无法进入桌面）：由于主板默认dvmt显存32M，所以FHD屏幕修改至少64M，QHD至少96M

#### 修改方法：

---------

**方式一**（推荐）：此方法有风险，目前bios在2.3.1到2.13.0中参数可用，其它自行测试

使用DVMT目录下的DVMT.efi引导启动，0x785是DVMT Pre-allocation，首先命令 `setup_var 0x785` 回车，查看有无返回值，未修改是返回0x01(32M),**如果没有返回值，切勿继续尝试以下操作**。其它2个参数也是类似命令，查看有无返回值，**没有返回值，切勿继续尝试以下操作**

之后修改以下三个参数`0x4de`  `0x785` `0x786` ,命令分别为：

 `setup_var 0x4de 0x00 ` 

 `setup_var 0x785 0x06 `  :这里我直接设置到192M,FHD设置`setup_var 0x785 0x02 `即可 

 `setup_var 0x786 0x03 ` 

options：

- setup_var 0x4DF 0x00 (default)
- setup_var 0x4DF 0x01 (battery life)
- setup_var 0x4DF 0x02 (performance)
- setup_var 0x64D 0x01
- setup_var 0x64E 0x01
- `0x653` -> `0x64` (CPU: -100 mV)
- `0x655` -> `01` (Negative voltage for `0x653`)
- `0x85A` -> `0x1E` (GPU: -30 mV)
- `0x85C` -> `01` (Negative voltage for `0x85A`)

| Variable              | Offset | Default value  | Desired value   | Comment                                                    |
| --------------------- | ------ | -------------- | --------------- | ---------------------------------------------------------- |
| CFG Lock              | 0x4de  | 0x01 (Enabled) | 0x00 (Disabled) | Disable CFG Lock to prevent MSR 0x02 errors on boot        |
| DVMT Pre-allocation   | 0x785  | 0x01 (32M)     | 0x06 (192M)     | Increase DVMT pre-allocated size to 192M for QHD+ displays |
| DVMT Total Gfx Memory | 0x786  | 0x01 (128M)    | 0x03 (MAX)      | Increase total gfx memory limit to maximum                 |

以上表格来自：[the-darkvoid](https://github.com/the-darkvoid/XPS9360-macOS)

教程：

1. 开机按F2或者F12进入BIOS，选择Boot Sequence![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvv0563xohj31kw16k7wn.jpg)
2. 点击Add Boot Option![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvv064tmofj31fq0xyu0z.jpg)
3. 填写名称，随便写什么都可以，选择DVMT.efi得路径，你可以把该文件放到U盘引导分区，也可以放到硬盘引导分区，随意，我的放在clover得tools中![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvv07927izj31bc12s1l0.jpg)
4. 完成后点击OK，保持BIOS设置，重启，按F12，选择之前的填写的引导后回车，我的是`SHELL`![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvv0bb4va8j30t60pukjl.jpg)
5. 分别输入以上三条命令，执行后会显示之前的值和设置后的值，结果如下图![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvv0f7eyyhj31ho0vg1kz.jpg)

--------

**方式二**：~~WhateverGreen貌似不支持10.14使用WhateverGreen修复DVMT-Prealloc 32MB,（已添加到配置文件中，正常可以生效，不需要修改BIOS的dvmt，我没有测试过，自行测试）~~

~~DVMT补丁在KextsToPatch中，默认添加，但是未开启，有需要自行打开~~

-----------------

### 2021-11-10 OpenCore（Monterey）

- OC 0.7.5
- kext

### 2021-03-03 OpenCore

- OC 0.6.7

### 2021-01-12 OpenCore

- OC 0.6.5

### 2020-11-24 OpenCore

- 修复Big Sur下电源键唤醒没有立刻亮屏
- 删除acpi亮度按键补丁替换为BrightnessKeys.kext

### 2020-11-07 OpenCore

- 日常更新OpenCore 0.6.3 & 最新驱动

### 2020-09-09 OpenCore

- 日常更新OpenCore 0.6.1 & 最新驱动

### 2020-08-10 

- 更新voodooi2c kext
- Macos 11.0以下版本使用老版本wtg，暂时解决10.15.6版本及以下睡眠唤醒后大概率卡顿（gpu满载造成的卡顿）

### 2020-08-04 更新OpenCore(0.60正式版)支持macOS Big Sur

- 常规更新0.60正式版和最新的驱动（voodinput.kext还是老版本和VoodooI2C不兼容），不再需要FakeSMC.kext和vsmcgen=1参数

### 2020-07-25 更新OpenCore支持macOS Big Sur（0.60测试版）

- 支持macOS 11 Beta3安装（默认使用VirtualSMC.kext，如果失败尝试使用FakeSMC.kext）
- macOS 10.15.6 也正常使用（boot-args删除vsmcgen=1，不删除影响不大）
- Clover引导不再更新（自己更新版本和驱动即可），最新的5120版本新增OC Quick可参考OC的配置

### 2020-04-17 添加OpenCore（0.57正式版）支持

- 此次添加了i7-8550U的OC支持，修改config名称即可，默认还是7560u，支持到10.15.4
- 参考[xxxz](https://github.com/xxxzc/xps15-9550-macos)修改acpi，所有acpi原始文件位于oc-acpi-dsdt目录，OC不建议使用重命名补丁，修改后删除大量重命名补丁
- 正常说OC的acpi和Clover通用，大家自行处理

### 2019-07-21（可能是我最后一次更新）

- 同步the-darkvoid配置，此次更新只支持iris 640核显，其他不再支持
- 兼容10.15测试版本，蓝牙驱动使用10.15文件夹内的驱动
- 更新smbios版的到最新
- Clover更新r5018
- 驱动更新到最新
- usb使用usbports.kext,蓝牙不内建，同时建议关闭蓝牙的所有高级选项，蓝牙比较稳定，很少出现不可用问题

### 2019-03-26

- 同步the-darkvoid配置
- 更新Clover到r4895
- 更新kext驱动

### 2018-12-22

- 使用最新版本WhateverGreen的framebuffer-flags补丁解决iris 640有时唤醒黑屏，不再使用仿冒，smbios还原为mbp14,1
- 更新ComboJack到最新，汉化选择耳麦提示，同时移除ALC256.aml，ComboJack不再需要
- 删除AppleBacklightFixup驱动，已经被WhateverGreen取代
- 更新Clover到最新4813
- 更新常规驱动到最新

### 2018-11-26

- 更新Clover到最新
- 添加ComboJack解决耳麦无法使用问题，最上方有使用教程

### 2018-11-05

- 更新USBPorts.kext解决无法识别一些3.0设备以及3.0设备识别后速度是480Mb/s而不是5Gb/s

### 2018-10-23

- 显卡声卡ID注入移动到dsdt中
- 显卡ID适合8代U，建议SMBIOS改为mbp14,1
- 更新Clover Configurator 到5.2.1.0
- 更新usb power属性注入
- 更新usbports.kext
- 添加原始hotpath文件，基于the-darkvoid修改

### 2018-10-13

- 更新clover版本到4700，移除一些不必要efi
- 使用AppleBacklightFixup.kext（基于Lilu）替代AppleBacklightInjector.kext(优点：可以去除亮度补丁，Recovery中工作，来自[hieplpvip](https://github.com/hieplpvip/AppleBacklightFixup)和[RehabMan](https://bitbucket.org/RehabMan/applebacklightfixup))
- ALC256 Layout-id从Device Properties注入（WhateverGreen支持）
- USBPower.kext更名为USBPorts.kext
- 使用4K主题Mojave4k,喜欢原来主题kuke的可以在config的theme修改即可
- 常规驱动更新

### 2018-09-27（支持10.14）

- 更新触摸板驱动VoodooI2C到V2.14兼容10.14

### 2018-09-21（可能是最后一次更新10.13.6，等待10.14正式版）

- Clover版本更新到4674
- 使用[VirtualSMC](https://github.com/acidanthera/VirtualSMC)代替[FakeSMC](https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek)
- 使用[WhateverGreen](https://github.com/acidanthera/WhateverGreen)方式驱动驱动显卡（依然是仿冒iris 540）
- 使用USBPower.kext替换[USB-Inject-All](https://bitbucket.org/RehabMan/os-x-usb-inject-all),同时去除SSDT-UIAC.aml（目前使用下来，蓝牙不像以前那样容易掉,有待观察）
- 更新[VoodooI2C](https://github.com/alexandred/VoodooI2C)版本到v2.1.1,支持所有原生触摸板手势（除了防误触外都很完美），驱动目前不兼容10.14，所以放在了kext/10.13中，10.14系统自行解决
- 更新驱动到最新版本

### 2018-09-06

- 更新最新版本Lilu和AppleAlc
- Fake id：iris 540（不影响性能），彻底解决有时候唤醒黑屏

### 2018-08-26

- 更新和精简驱动
- clover更新版本到v4658

### 2018-07-22

- 更新clover到r4618
- 更新smbios信息
- 更新部分hotpatch文件
- 更新lilu以及一波驱动，替换[IntelGraphicsFixup](https://github.com/lvs1974/IntelGraphicsFixup)和[Shiki](https://github.com/acidanthera/Shiki)为[WhateverGreen](https://github.com/acidanthera/WhateverGreen)

### 2018-06-07

- 尝试修复iris 640有时唤醒黑屏问题，参考[syscl](https://github.com/syscl/XPS9350-macOS)的iris 540补丁
- 更新smbios部分信息

### 2018-05-28

- Clover更新到4497
- 部分驱动更新到最新版本

### 2018-03-31

- 精简无用项
- 暂时移除蓝牙相关驱动,可能导致无法启动

### 2018-03-17

- 更新clover到4418
- 雷电3可以热插拔了
- 触摸板替换为VoodooI2C，使用更加灵敏
- Applealc，IntelGraphicsFixup，Lilu驱动版本更新
- 关于无用启动引导也好了，clover configurator版本问题，导致配置有问题


### 2018-01-24

- 更新clover到4380
- 更新hotpatch文件，无需注入显卡id，自动识别，同样也支持其它cpu
- 更新AppleALC最新release版
- 加入shiki.kext驱动，修复itunes 无故退出
- 更新主题Kuke
- clover界面多了很多无用的启动项，不知道为什么无法屏蔽！！！

## Credit
- [the-darkvoid](https://github.com/the-darkvoid/XPS9360-macOS)

- [RehabMan](https://bitbucket.org/RehabMan/)

- [Lilu](https://github.com/acidanthera/Lilu) And [Lilu plugins](https://github.com/acidanthera/Lilu/blob/master/KnownPlugins.md)
