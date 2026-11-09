# g83-11gen-hackintosh-tahoe-efi

> **本机的黑苹果方案被 B 站 UP 主「老张是大佬」点名**（见下图，字幕里的群友五菱宏光s 就是我），在此存个纪念：一个垃圾佬 DIY 的完整成果。
>
> ![老张点名的五菱宏光s](docs/laozhang-mention.jpeg)

东芝 dynabook G83/HS（11 代 Tiger Lake）黑苹果 EFI，目标系统 macOS Tahoe（26.x）。基于 OpenCore 1.0.8 RELEASE。

## 1. 最低系统要求

- **macOS Tahoe 26.7 或更新**（上游 WhateverXe 对 Tahoe 的门槛即 26.7+；本机 26.7.2 实测通过）
- OpenCore 1.0.8 RELEASE 或等价版本

## 2. 机型信息

| 项目 | 值 |
|------|-----|
| 机器 | Toshiba dynabook G83/HS（Tiger Lake U 笔记本） |
| CPU | Intel Core i5-1135G7 |
| 核显 | Iris Xe 80EU（`0x9A49`），WhateverXe + Lilu 注入驱动 |
| 网卡 | Intel Wi-Fi 6 + 蓝牙 + 有线 |
| 声卡 | Realtek ALC257 |
| SMBIOS | MacBookPro16,2 |
| 系统 | macOS Tahoe 26.x |

不适配其他机型的部分（SSDT / USB 定制表 / WEX 参数）请按需自行调整。

## 3. 正常功能

| 功能 | 状态 | 说明 |
|------|------|------|
| Wi-Fi | ✅ | AirportItlwm（Intel 无线网卡） |
| 蓝牙 | ✅ | IntelBluetoothFirmware + BlueToolFixup |
| 有线网卡 | ✅ | IntelMausi |
| USB | ✅ | USBToolBox + UTBMap 定制端口表 |
| 内屏（息屏）唤醒 | ✅ | 熄屏后可正常恢复 |
| 内屏亮度 | ✅ | 已修复开局亮度偏低 |
| `Fn` 亮度快捷键 | ✅ | BrightnessKeys |
| 声卡（扬声器 / 耳机） | ✅ | ALC257 |
| 电池状态 | ✅ | SMCBatteryManager |
| GPU 加速（Metal） | ✅ | WhateverXe 注入驱动 |
| H.264 / HEVC 硬解 | ✅ | 核显媒体引擎 |
| 外接 HDMI 4K@60Hz | ✅ | 稳定无黑闪 |
| 摄像头 | ✅ | 内置摄像头 |
| 外接 Type-C 扩展坞视频输出 | ✅ | 实测 4K@30Hz |

## 4. 已知问题

| 问题 | 现状 / 处理 |
|------|------------|
| 内置麦克风 | 不工作，使用外接或耳机麦克风 |
| 无法睡眠唤醒 | 通过禁用系统睡眠解决，执行以下命令即可： |

```bash
sudo pmset -a sleep 0
sudo pmset -a hibernatemode 0
sudo pmset -a disablesleep 1
```

## 5. WEX（WhateverXe）改进点

在本机使用的开源图形驱动 [WhateverXe](https://github.com/b00t0x/WhateverXe) 基础上做的下游修复（完整细节见 [WhateverXe-Dynabook](https://github.com/xiananliu/WhateverXe-Dynabook)）：

| 解决的问题 |
|-----------|
| 外接 HDMI 可输出 4K@60Hz |
| 4K@60 黑闪（每几秒黑屏数秒）根治 |
| 内屏 eDP 偶发不亮／点亮后失效 |
| 开机亮度偏低 |
| 熄屏后无法唤醒 |
| 本机核显（80-EU）在此驱动下加速可用 |

排障用探针与实验项已从发布版配置中移除，不属于本仓库内容。

## 6. 使用说明

1. 用磁盘工具（或 `diskutil mount diskXs1`）挂载 EFI 分区，把本仓库的 `EFI` 覆盖过去（机器自带的 Windows 引导目录可保留不动）。
2. BIOS 设置：关闭 **Secure Boot**即可启动（其他项保持默认）。
3. **三码替换（必做一次）**：`config.plist` 的 `PlatformInfo→Generic` 中 `SystemSerialNumber / MLB / SystemUUID / ROM` 均为空——请用 [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) 选择 `MacBookPro16,2` 生成后再填入；`ROM` 填 12 位随机 MAC，或从另一台真 Mac 抓取（iMessage 更容易通）。**请勿直接使用网络/他人已发布的 serial**。
4. **安装核显运行组件（必装）**：第一次成功进入系统后，到 [WhateverXe Graphics Releases](https://github.com/b00t0x/WhateverXe-Graphics/releases) 下载最新 **pkg** 安装，然后重启——不装的话核显加速不完整。

## 7. 关联项目

| 项目 | 说明 |
|------|------|
| [OpenCore](https://github.com/acidanthera/OpenCorePkg) | 引导程序（本仓库用 1.0.8 RELEASE） |
| [Lilu](https://github.com/acidanthera/Lilu) | 内核补丁框架 |
| [WhateverXe](https://github.com/b00t0x/WhateverXe) | 本机核显驱动的上游（BSD-3-Clause，本仓库 LICENSE 继承其许可体系） |
| [WhateverXe Graphics](https://github.com/b00t0x/WhateverXe-Graphics) | 核显运行组件（首次进系统必装，见使用说明第 4 条） |
| [WhateverXe-Dynabook](https://github.com/xiananliu/WhateverXe-Dynabook) | 本机使用的 WhateverXe 下游修改版（驱动改进细节见其 README） |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC) | SMC 模拟（含电池/传感器插件） |
| [AppleALC](https://github.com/acidanthera/AppleALC) | 声卡驱动 |
| [AirportItlwm](https://github.com/OpenIntelWireless/itlwm) | Intel Wi-Fi 驱动 |
| [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) | Intel 蓝牙驱动 |
| [IntelMausi](https://github.com/acidanthera/IntelMausi) | Intel 有线网卡驱动 |
| [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C) / [VoodooRMI](https://github.com/1Revenger1/VoodooRMI) / [VoodooPS2Controller](https://github.com/acidanthera/VoodooPS2) | 触摸板 / 键盘输入栈 |
| [USBToolBox](https://github.com/USBToolBox/kext) | USB 端口定制 |
| [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys) | 亮度快捷键 |
| [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) | 三码生成工具（使用说明第 3 条用到） |

本仓库许可见 [LICENSE](LICENSE)（BSD-3-Clause）；仓库内 OpenCore / kext 等二进制组件版权仍归各自上游。

## 8. 版本与更新

EFI 目录每次更新会自动打 tag 并发布到 [Releases](https://github.com/xiananliu/g83-11gen-hackintosh-tahoe-efi/releases)（附打包好的 EFI zip 与校验文件），变更见各 release 说明与提交记录。
