# 英特尔BIOS设置
- 注意：这些选项中的大多数可能不存在于固件中，建议尽可能地进行匹配，但如果这些选项中的许多选项在BIOS中不可用，也不要太在意。

## 关闭
- Fast Boot
- Secure Boot
- Serial/COM Port
- Parallel Port
- VT-d （如果将 `DisableIoMapper` 设置为 `YES` ，则可以开启）
- CSM （或者 UEFI Only）
- Thunderbolt (对于初始安装，如果未正确设置，Thunderbolt 可能会导致问题）
- Intel SGX
- Intel Platform Trust
- CFG Lock （MSR 0xE2 写入保护）（**黑苹果无法在启用CFG-Lock时启动。**）
  - **这必须关闭，如果找不到该选项则需要启用 `Kernel` -> `Quirks` 下的`AppleXcpmCfgLock` 。**

## 开启
- VT-x
- Above 4G decoding
  - 2020年+ BIOS 注意：启用 Above4G 时，Resizable BAR Support 可能会在某些Z490和更新的主板上可用。如果启用，请确保将 Booter -> Quirks -> ResizeAppleGpuBars 设置为 `0` 。
- Hyper-Threading
- Execute Disable Bit
- EHCI/XHCI Hand-off
- OS type: Windows 8.1/10 UEFI Mode
- DVMT Pre-Allocated(iGPU Memory): 64MB
- SATA Mode: AHCI

# 前置Windows步骤

## 7-Zip or Bandizip
[7-Zip](https://www.7-zip.org)

[Bandizip](https://www.bandisoft.com)

## Python
[Python](https://www.python.org)

# DSDT

## SSDTTime
>虽然通用文件很方便，但是使用工具提取出来的文件的适配性和兼容性更高

[SSDTTime](https://github.com/corpnewt/SSDTTime)

### 提取文件
>愿原力与你同在

运行 `SSDTTime.bat` ，选择 `Dump DSDT` 生成 `DSDT.aml` 文件

回到主菜单，选择 `FakeEC` 生成 `SSDT-EC.aml`、`SSDT-EC.dsl` 文件

回到主菜单，选择 `PluginType` 生成 `SSDT-PLUG.aml`、`SSDT-PLUG.dsl` 文件

回到主菜单，选择 `AWAC` 生成 `SSDT-AWAC.aml`、`SSDT-AWAC.dsl` 文件

回到主菜单，选择 `USB Reset` 生成 `SSDT-USB-Reset.aml`、`SSDT-USB-Reset.dsl` 文件

### 下载文件
在 `SSDTTime\Scripts` 目录下，下载 [SSDT-USBX.aml](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/SSDT-USBX.aml) 文件

### 反编译与编译
#### 反编译
在 `SSDTTime\Scripts` 目录下使用 `cmd.exe` 运行 `iasl SSDT-USBX.aml` 反编译生成 `SSDT-USBX.dsl` 文件

将 `SSDT-USBX.dsl` 文件内的 `Device (USBX)` 内容复制到 `SSDT-EC.dsl` 文件内
- `Device (USBX)` 内容不包含其下的 `Method (_STA, 0, NotSerialized)  // _STA: Status` 部分
- 在 `Device (EC)` 的尾部空两行再插入 `Device (USBX)` 的内容

#### 重命名
将 `SSDT-EC.dsl` 重命名成 `SSDT-EC-USBX.dsl`

#### 编译
在 `SSDTTime\Scripts` 目录下使用 `cmd.exe` 运行 `iasl "SSDTTime\Results\SSDT-EC-USBX.dsl"` 编译生成 `SSDT-EC-USBX.aml` 文件

### 重命名
将 `SSDT-USB-Reset.aml` 重命名成 `SSDT-RHUB.aml`

# U盘
## 工具
下载 [gibMacOS](https://github.com/corpnewt/gibMacOS)

运行 `gibMacOS.bat`，选择 `Toggle Recovery-Only` ，选择带有 `- FULL Install` 字样的选项下载系统恢复镜像

## 插入U盘

## 写入U盘
>愿原力与你同在

运行 `MakeInstall.bat`，选择U盘和 `Use OpenCore instead of Clover` 选项，例如 `1O`、`2O`、`3O` 等

运行时需要输入系统恢复镜像的文件路径

# OpenCore
解压提取 `X64` 目录下的 `EFI` 文件夹

`BOOT` 文件夹内删除 `.contentFlavour` 文件

`OC` 文件夹内删除 `.contentFlavour` 文件

视情况删除 `OC` 文件夹内的 `Resources` 文件夹

## ACPI
将 `SSDT-PLUG.aml`、`SSDT-EC-USBX.aml`、`SSDT-AWAC.aml`、`SSDT-RHUB.aml` 移动到 `ACPI` 目录

## Drivers
只留下 `AudioDxe.efi`、`OpenRuntime.efi` 文件，其他的都删掉

在 `Drivers` 目录下，下载 [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)

## Kexts
[VirtualSMC](https://github.com/acidanthera/VirtualSMC)
- VirtualSMC.kext
- SMCProcessor.kext
- SMCSuperIO.kext

[Lilu](https://github.com/acidanthera/Lilu)

[WhateverGreen](https://github.com/acidanthera/WhateverGreen)

[AppleALC](https://github.com/acidanthera/AppleALC)

~~[USBInjectAll](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads)~~（原版，但是自OpenCore 0.7.4后不再适配）  <br>
[USBInjectAll](https://github.com/daliansky/OS-X-USB-Inject-All)（由[黑果小兵](https://blog.daliansky.net)进行适配）
- USB定制后可删除

[AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup)
- 强制使用 `brcmfx-driver=` 加载特定的驱动程序可能会有所帮助
  - 以 BCM94352Z 为例，可能需要在 `boot-args` 中 使用`brcmfx-driver=2` 解决问题，其他芯片组将需要其他参数

[BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM)
- BrcmBluetoothInjector.kext (MacOS 12-)
- BrcmFirmwareData.kext
- BrcmPatchRAM3.kext
- BlueToolFixup.kext (MacOS 12+)
  - 不可在 macOS 12 或更新的系统下使用 `BrcmBluetoothInjector.kext`

## config.plist
将 `Docs\Sample.plist` 文件移动到 `EFI/OC/` 目录下，并且重命名为 `config.plist`

### 下载 ProperTree
[ProperTree](https://github.com/corpnewt/ProperTree)

### 编辑 config
>可以与旧版本的配置文件对比再修改

>当前步骤中没有独立显卡，输出画面是由集成显卡输出的

运行 `ProperTree.bat` -> `File` -> `Open` -> 选择 `EFI/OC/config.plist`

点击 `File` -> `OC Clean Snapshot` -> 选择 `EFI/OC` 目录

删除所有 `#WARNING` 字符

Booter -> Quirks -> DevirtualiseMmio -> true  <br>
Booter -> Quirks -> EnableWriteUnprotector -> false  <br>
Booter -> Quirks -> ProtectUefiServices -> true  <br>
Booter -> Quirks -> RebuildAppleMemoryMap -> true  <br>
Booter -> Quirks -> SetupVirtualMap -> false  <br>
Booter -> Quirks -> SyncRuntimePermissions -> true

DeviceProperties -> 右键 `Add` 条目 -> 在 `Add` 条目下创建 `PciRoot(0x0)/Pci(0x2,0x0)` Dictionary  <br>
DeviceProperties -> Add -> 右键 `PciRoot(0x0)/Pci(0x2,0x0)` 条目 -> 在 `PciRoot(0x0)/Pci(0x2,0x0)` 条目下创建 `AAPL,ig-platform-id` Data -> 值为 `00009B3E`
- AAPL,ig-platform-id = `00009B3E` 只支持集显，独显输出则设置为 `0300C89B`
- `framebuffer-patch-enable` 与 `framebuffer-stolenmem` 需要 BIOS 内的 `DVMT Pre-Allocated` 少于 64MB 或者没有该选项

Kernel -> Quirks -> PanicNoKextDump -> true  <br>
Kernel -> Quirks -> PowerTimeoutKernelPanic -> true  <br>
Kernel -> Quirks -> XhciPortLimit -> true （运行 MacOS 11.3+ 则关闭）

Misc -> Debug -> AppleDebug -> true  <br>
Misc -> Debug -> ApplePanic -> true  <br>
Misc -> Debug -> DisableWatchDog -> true  <br>
Misc -> Debug -> Target -> `3` （若需文件形式输出日志信息，则改为 `67`）  <br>
Misc -> Security -> AllowSetDefault -> true  <br>
Misc -> Security -> ScanPolicy -> `0`（[Opencore ScanPolicy Generator](https://github.com/i3p9/oc-scanpolicy) 生成所需的值，OpenCore默认为 `17760515`）  <br>
Misc -> Security -> SecureBootModel -> `j185f`  <br>
Misc -> Security -> Vault -> `Optional`

NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82-> boot-args -> `debug=0x100 keepsyms=1 -v alcid=1 igfxonln=1 -igfxvesa`  <br>
NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82-> prev-lang:kbd -> ``
- 删除 `#INFO (prev-lang:kbd)` 条目与 `en:252 (ABC), set 656e3a323532` 字符

PlatformInfo -> Generic -> MLB ->   <br>
PlatformInfo -> Generic -> ROM ->   <br>
PlatformInfo -> Generic -> SystemProductName -> iMac20,2  <br>
PlatformInfo -> Generic -> SystemSerialNumber ->   <br>
PlatformInfo -> Generic -> SystemUUID -> 

UEFI -> APFS -> MinDate -> 20200306  <br>
UEFI -> APFS -> MinVersion -> 1412101001000000  <br>
UEFI -> Audio -> AudioOutMask -> 未知  <br>
UEFI -> Audio -> PlayChime -> `Auto` / `Enabled` （未测试）  <br>
UEFI -> Input -> PointerSupportMode -> ``

## 替换文件
移动或替换 `EFI` 文件夹到U盘

# 启动
>硬盘必须是`GPT` 或 `GUID` 分区类型

## U盘启动
从U盘启动 MacOS 系统，并且测试是否正常

确保有线网络与无线网络至少有一个可以正常连接互联网

选择 `Disk Utility` 然后 `Continue`，选择安装 MacOS 系统所需的内置硬盘

选择 `Erase` ，重命名为 `Macintosh HD`，格式为 `APFS`，点击 `Erase`

回到恢复主界面，点击 `Reinstall macOS`、`Continue`、`Install`、`Install`

选择系统硬盘，点击 `Install`

等待一段时间，进入系统初始化界面

系统初始化完毕后，确认是否可以正常进入系统

## 移动 EFI 目录
打开 `Terminal` 

输入 `sudo diskutil list` 后回车，输入密码

显示出所有硬盘，找到 `TYPE` 和 `NAME` 都是 `EFI` 的硬盘，复制其 `IDENTIFIER` 值 (举例 `disk0s1`)

输入 `sudo diskutil mount ` 与硬盘的 `IDENTIFIER` 值 (举例 `sudo diskutil mount disk0s1`)，然后回车

将U盘内的 `EFI` 文件夹（在名为`BOOT`的硬盘下）移动或替换到新出现的 `EFI` 硬盘内

弹出U盘，测试系统重启与运行是否正常

# Hackintool

## 下载
[Hackintool](https://github.com/headkaze/Hackintool)
- `应用补丁` -> `应用补丁` -> `设备属性` -> `接口`, `基本显存`, `图形卡`, `音频`

## ~~修复HDMI~~
>~~DP总线类型：2、4、5、6  <br>~~
>~~HDMI总线类型：1、2、4、6~~

~~应用补丁 -> 接口 -> 红色则为DP接口，明确`索引`和`总线ID`~~

~~将剩余条目的`类型`由`DP`改为`HDMI`，修改总线ID~~

~~点击屏幕左上角`文件` -> `导出` -> `引导工具 Config.plist`~~
- ~~(con0~2) * busID(1,2,4,6)~~

~~选择 `EFI/OC` 目录，确认替换 `config.plist`~~

~~重启系统并测试 HDMI~~

因为10代CPU (Comet Lake) 搭配Z590系列主板无法驱动板载视频输出，  <br>
所以HDMI接口与DP接口都不能正常地输出视频信号，  <br>
NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82-> boot-args -> 加入 ` -igfxvesa`  <br>
加入 ` -igfxvesa` 以进入系统进行调试等设置，HDMI与DP均能显示，**无需定制、修复**，**也无法修复**（但显存为7MB）  <br>
- 强制 GPU 进入 VESA 模式（无 GPU 加速），对故障排除很有用
- ` -igfxvesa` 仅作调试用途，不建议在工作环境下使用

在后续步骤中包含接入独立显卡，接入并调试完成独立显卡后，可删除 ` -igfxvesa`

## 修复音频
查看 [AppleALC](https://github.com/acidanthera/AppleALC) 以找到与 `Realtek® ALC1220P` 同型号或相近型号的编解码器

ALC1220 修订和布局分别为 `0x100101, 0x100003, layout 1, 2, 3, 5, 7, 11, 13, 15, 16, 17, 21, 27, 28, 29, 30, 34, 35, 98, 99, 100`  <br>
ALC1220 最小内核为 `12 (10.8)`

音频 -> 声音信息 -> 名称 -> `ALC Layout ID` -> 改变和测试该值 (举例 `5`)  <br>
(DeviceProperties -> Add -> `PciRoot(0x0)/Pci(0x1F,0x3)` -> `layout-id` -> `05000000`)

点击屏幕左上角`文件` -> `导出` -> `引导工具 Config.plist`

重启系统, 屏幕左上角苹果图标，系统偏好设置，声音, 检查声音是否正常

删除 config.plist 内的 `NVRAM` -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82-> boot-args -> `alcid=1`

视情况删除 `DeviceProperties` -> Add -> `PciRoot(0x0)/Pci(0x1b,0x0)` 条目

## 修复USB
USB -> 绿色条目代表插入了USB设备

名称包含`HS`代表USB2.0，包含`SS`代表USB3.0

USB3.0接口需要USB3.0、USB2.0设备各插拔一次，使用过的接口的条目变绿

对不变色的条目，点击下方`-`号删除
- 若删除USB3.0接口的USB2.0条目，实际USB3.0接口不再兼容USB2.0

减少到15个为止

USB -> USB接口 -> 连接器 -> 分别设置`USB2`或`USB3`

USB -> USB接口 -> 连接器 -> `Bluetooth USB Host Controller`设备 `连接器`设置为`Internal`

点击下方`导出`符号进行导出

ProperTree 导入 `SSDT-UIAC.aml` 文件并保存  <br>
Kernel -> Quirks -> XhciPortLimit -> false
- 使用 `.kext` 文件也能修复USB，但是可能需要更多的别的设置
- 视情况停用并删除 `USBInjectAll.kext`

重启系统并且测试

# Big Sur 修复
AirportBrcmFixup
- 强制使用 `brcmfx-driver=` 加载特定的驱动程序可能会有所帮助
  - 以 BCM94352Z 为例，可能需要在 `boot-args` 中 使用`brcmfx-driver=2` 解决问题，其他芯片组将需要其他参数

Kernel -> Quirks -> XhciPortLimit -> false

SATA 支持受损
- 由于 Apple 在 AppleAHCIPort.kext 中 删除了 AppleIntelPchSeriesAHCI 类
- 解决这一问题，添加 Catalina 的补丁 [AppleAHCIPort.kext](https://github.com/dortania/OpenCore-Install-Guide/blob/master/extra-files/CtlnaAHCIPort.kext.zip) 并将 `MinKernel` 设为 `20.0.0`
- 不能进行SATA热插拔，**Monterey 系统似乎不需要修复**（未测试，没有SATA设备）

# Monterey 修复
>[BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM)

BrcmBluetoothInjector.kext
- 如果仍然启动 Big Sur 及更早版本的系统，应在 config.plist 中将该 kext 的 `MaxKernel` 字段设置 `20.99.9`（默认为 ``）

保留 BrcmFirmwareData.kext  <br>
保留 BrcmPatchRAM3.kext

加入  <br>
[BlueToolFixup](https://github.com/acidanthera/BrcmPatchRAM)
- 如果仍然启动 Big Sur 及更早版本的系统，应在 config.plist 中将该 kext 的 `MinKernel` 字段设置 `21.00.0` （默认为 ``）以防止在旧系统上加载 BlueToolFixup

在 `boot-args` 中加入 ` e1000=0`

# 其他
