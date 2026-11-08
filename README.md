# g83-11gen-hackintosh-tahoe-efi

东芝 dynabook G83/HS（11 代 Tiger Lake）黑苹果 EFI，目标系统 macOS Tahoe（26.x）。基于 OpenCore 1.0.8 RELEASE。

## 1. 最低系统要求

- **macOS Tahoe 26.7 或更新**（上游 WhateverXe 对 Tahoe 的门槛即 26.7+；本机在 26.7.x 实测通过）
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

本机在开源 [WhateverXe](https://github.com/b00t0x/WhateverXe) 0.7.0 基础上做的下游改进（完整细节见 [WhateverXe-Dynabook](https://github.com/xiananliu/WhateverXe-Dynabook)）：

| 改进 | 解决的问题 |
|------|-----------|
| HDMI 模式上限 300→600MHz | 外接 HDMI 可输出 4K@60Hz |
| SCDC scrambling 写入（sink 端握手修正） | 4K60 黑闪根治 |
| CDCLK 修正 | 内屏 eDP 黑屏（概率性不亮） |
| 背光量纲修正 | 开机亮度偏低 |
| DC6/DC9 深断电绕过 | 熄屏后无法唤醒 |
| 80-EU 核显资源剖面修正 | 本机核显不被上游白名单拒绝，GPU 加速可用 |

排障/实验用的探针与数据分析项已从发布版配置中移除，不属于本仓库内容。

## 6. 使用说明

1. 用磁盘工具（或 `diskutil mount diskXs1`）挂载 EFI 分区，把本仓库的 `EFI` 覆盖过去（机器自带的 Windows 引导目录可保留不动）。
2. BIOS 设置：关闭 **Secure Boot**即可启动（其他项保持默认）。
3. **三码替换（必做一次）**：`config.plist` 的 `PlatformInfo→Generic` 中 `SystemSerialNumber / MLB / SystemUUID / ROM` 均为空——请用 [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) 选择 `MacBookPro16,2` 生成后再填入；`ROM` 填 12 位随机 MAC，或从另一台真 Mac 抓取（iMessage 更容易通）。**请勿直接使用网络/他人已发布的 serial**。
