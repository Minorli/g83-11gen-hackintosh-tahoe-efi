# g83-11gen-hackintosh-tahoe-efi

东芝 dynabook G83/HS（11 代 Tiger Lake）黑苹果 EFI，目标系统 macOS Tahoe（26.x）。基于 OpenCore 1.0.8 RELEASE。

## 1. 机型信息

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

## 2. 正常功能

| 功能 | 状态 | 说明 |
|------|------|------|
| Wi-Fi | ✅ | AirportItlwm（Intel 无线网卡） |
| 蓝牙 | ✅ | IntelBluetoothFirmware + BlueToolFixup |
| 有线网卡 | ✅ | IntelMausi |
| USB | ✅ | USBToolBox + UTBMap 定制端口表 |
| 内屏（息屏）唤醒 | ✅ | `-wex-dcoff` 保证熄屏后可恢复 |
| 内屏亮度 | ✅ | `-wex-blfix` 根治开局亮度偏低 |
| `Fn` 亮度快捷键 | ✅ | BrightnessKeys |
| 声卡（扬声器 / 耳机） | ✅ | ALC257，`alcid=11` |
| 电池状态 | ✅ | SMCBatteryManager |
| GPU 加速（Metal） | ✅ | WhateverXe 注入驱动 |
| H.264 / HEVC 硬解 | ✅ | 核显媒体引擎 |
| 外接 HDMI 4K@60Hz | ✅ | 稳定无黑闪（`-wex-hdmi-clock600` + `-wex-hdmi-scdc-write`） |

## 3. 已知问题

| 问题 | 现状 / 处理 |
|------|------------|
| 内置麦克风 | 不工作，使用外接或耳机麦克风 |
| 无法睡眠唤醒 | 通过禁用系统睡眠解决，执行以下命令即可： |

```bash
sudo pmset -a sleep 0
sudo pmset -a hibernatemode 0
sudo pmset -a disablesleep 1
```

## 4. 使用说明

1. 用磁盘工具（或 `diskutil mount diskXs1`）挂载 EFI 分区，把本仓库的 `EFI` 覆盖过去（机器自带的 Windows 引导目录可保留不动）。
2. BIOS 设置：关闭 **Secure Boot**即可启动（其他项保持默认）。
3. **三码替换（必做一次）**：`config.plist` 的 `PlatformInfo→Generic` 中 `SystemSerialNumber / MLB / SystemUUID / ROM` 均为空——请用 [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) 选择 `MacBookPro16,2` 生成后再填入；`ROM` 填 12 位随机 MAC，或从另一台真 Mac 抓取（iMessage 更容易通）。**请勿直接使用网络/他人已发布的 serial**。
4. BOOT-ARGS 已按最小集配置，一般无需添加；如遇排障可临时加上 `-wex-hdmi-diag` 等诊断参数（见 [WhateverXe-Dynabook 仓库](https://github.com/xiananliu/WhateverXe-Dynabook) 的 README）。
