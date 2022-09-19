

# XPS 13-9360 Monterey

| Category                | Details                                                     |
| ------------------- | ------------------------------------------------------------ |
| Computer Model            | DELL XPS 13-9360                                             |
| Clover:Current System Version | macOS Mojave 10.14.6(18G84) & 10.15(Beta 3)                  |
| OC:Current System Version     | macOS Monterey 12.0.1 (21A559)                               |
| BIOS                | 2.13.0 (2.6.2 Later versions have bugs and the memory frequency becomes 1867 MHz, but it does not affect the installation) |
| Processor              | Intel Core i7-7560U/i7-8550U                                 |
| Memory                | 16 GB(DDR3L 2133 MHz)                                        |
| Hard disk                | SAMSUNG PM961 (512GB)                                        |
| Graphics card                | Intel Iris Plus Graphics 640/UHD 620                         |
| Monitor              | QHD(Sharp touch screen 3200x1800)                                     |
| Sound card                | ALC256 (ALC3246)                                             |
| Network card                | replaced with BCM94360cs2 (Original network card Killer 1535，can also be replaced with DW1560/1830/1820A, otherwise, the network card cannot be driven, and there are some problems with Bluetooth.) |

![](https://hoan-pic.oss-cn-hangzhou.aliyuncs.com/img/info.png)

## Notes after system installation

### Headphone

Method 1：ALCPlugFix file(From:[daliansky黑果小兵](https://github.com/daliansky/dell7000)）, Double-click ALCPlugFix/install.command，But I can't use the headset when I plug it in，The computer used has a built-in mic



Method 2 (Recommend)：ComboJack(From：[ohmygod1993](http://bbs.pcbeta.com/viewthread-1799183-1-1.html),[hackintosh-stuff](https://github.com/hackintosh-stuff/ComboJack)，Modify the driver on xps9560

1. First，if used ALCPlugFix method，Uninstall first，Double-click ALCPlugFix/uninstall.command，And delete CLOVER⁩ ▸ ⁨kexts⁩ ▸ ⁨Other⁩ under the CodecCommander.kext driver

2. 将kexts⁩ ▸ ⁨ComboJack_Installer⁩Put the VerbStub.kext under CLOVER⁩ ▸ ⁨kexts⁩ ▸ ⁨Under Other⁩

3. Execute in the terminal⁨ kexts⁩ ▸ ⁨ComboJack_Installer⁩ install.sh, and restart

4. Insert headphones and select headset, so that both headphones and headsets shouldwork normally



### Network Card

1. If the wireless frequency band is not enough, it can be added in the boot parameters in config `brcmfx-country=#a`, and restart

2. The latest version of AirportBrcmFixup shows that the network card is a third-party under DW1830. It has no effect. You can use version 1.1.6 to restore it
3. Apple's original network card needs to add the AirportBrcmFixup driver, otherwise sleep will sometimes be woken up



### Monitor

If the QHD resolution device, the Apple logo becomes larger in the second stage of booting, fill in `2` in the UIScale of Boot Graphics of config, and restart.

FHD or external display needs to enable HIDPI，You can refer to [xzhih/one-key-hidpi](https://github.com/xzhih/one-key-hidpi)

Color calibration file：Displays/RXN49_LQ133Z1_01.icm, The file is a QHD screen color calibration file（From：[grawlinson](https://github.com/grawlinson/dell-xps-9360/tree/master/display)，Copy to `/Users/<username>/Library/ColorSync/Profiles`or`/Library/ColorSync/Profiles`then select Colors in Display Preferences



### Bluetooth

Regarding the Bluetooth problem, put the BrcmFirmwareData.kext and BrcmPatchRAM2.kext drivers in the Bluetooth directory into the corresponding driver directory of the clover or put BrcmFirmwareRepo.kext and BrcmPatchRAM2.kext in the system L/E directory and rebuild the cache. The official explanation is that they are placed in the system. This kind of memory is more efficient, and I can't see the difference at present. BT4LEContiunityFixup.kext is to repair the Handoff function. I don't need it. I didn't add it. I tested it myself. 10.15 uses the 10.15 folder driver.

If the card has been replaced, Solve it by yourself, The latest Bluetooth download address：https://github.com/acidanthera/BrcmPatchRAM/releases



### WIFI

Regarding the WIFI problem, if the WIFI cannot be driven, add the driver in the WIFI directory, DW1830 does not need it, DW1560 may need it



### CPU

With low frequency，It is recommended to use [stevezhengshiqi/one-key-cpufriend](https://github.com/stevezhengshiqi/one-key-cpufriend) The script is customized according to your needs，There are many options

or the following script
```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/stevezhengshiqi/one-key-cpufriend/master/one-key-cpufriend_cn.sh)"
```



### USB

There are currently two driving methods: RM's usbinjectall+uiac and the USBPort generated based on fbPatcher.app. I don't see which one is better. I prefer the first one.

The first is also the default way to use RM's usbinjectall method, put SSDT-UIAC.aml of kext/USB/usbinjectall under CLOVER/ACPI/patched, USBInjectAll.kext under CLOVER/kexts/Other, And delete USBPorts.kext, reboot

The second method, the current default method, has a drawback, that is, after modifying smbios, all subs will fail, and you need to modify USBPorts.kext. I added a kext/USB/usbport/mbp14,1/USBPorts.kext by default, this supports The smbios is mbp14,1, just replace the original one. If you want to change other smbios, the tutorial is as follows:

1. Right-click USBPorts.kext to display the package contents
2. Just open the Info.plist of Contents with any text tool and modify a few pieces of information.



### Camera

> ProductID_22155   	(0x568b)
>
> VendorID_3034      	(0x0bda Realtek Semiconductor Corp.)

10.15 and above systems can be used normally without adding a driver, but there are differences in camera hardware, with different device IDs, some devices may not be available, you can try to add one of the following drivers (under the kexts directory)

[UVC2FaceTimeHD.kext](./kexts/UVC2FaceTimeHD.kext)

[FaceTimeHD.kext](./kexts/FaceTimeHD.kext)



### SD Card Driver

You can try the following drivers, because I don't have the relevant equipment, you can test it yourself

[cholonam/Sinetek-rtsx ](https://github.com/cholonam/Sinetek-rtsx/releases)

### Rebuild cache

```
# Rebuild cache
sudo kextcache -i /

# Changed L/E or S/L/E driver for some reason
sudo chown -R root:wheel /System/Library/Extensions/
sudo chmod -R 755 /System/Library/Extensions/
sudo kmutil install --update-all
sudo kcditto
```

### Sleep Mode (Better Sleep)

```
sudo pmset -a hibernatemode 0
sudo pmset -a autopoweroff 0
sudo pmset -a standby 0
sudo rm /private/var/vm/sleepimage
sudo touch /private/var/vm/sleepimage
sudo chflags uchg /private/var/vm/sleepimage
```



### Known Issues

- ~~SD card reader cannot be used~~ (BIOS can be turned off to save power)
- ~~The lightning port needs to be plugged in before it is turned on or before waking up from sleep, and the cold boot will take effect (the hot swap is normal after it takes effect)~~
- ~~Headphone microphone does not work (computer microphone is normal, use computer microphone when plugging in headphones)~~
- ~~The headset is not unplugged before sleep. After waking up, the headset may be silent. Just unplug it again~~ (ComboJack may also fix this)
- ~~Bluetooth waking up after a long sleep may not work, you may need to wake up from sleep again or restart~~ (The new version of usbports.kext does not have built-in bluetooth, and turn off the advanced options of bluetooth, which greatly improves the problem, and the probability of problems is very small)
- ~~Intel Iris Plus Graphics 640 has a chance of black screen after waking up from sleep~~, Fixed the framebuffer-flags patch of WhateverGreen


### BIOS recommended settings

- Sata: AHCI
- Disable Secure Boot
- Enable VT
- Disable VT-D
- Disable SD card reader (Optional, turn off to save 0.5w power consumption)

---------

#### Installation precautions (very important, otherwise you may not be able to enter the desktop): Since the default dvmt video memory of the motherboard is 32M, the FHD screen is modified at least 64M, and the QHD screen is at least 96M

#### Modification method:

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
