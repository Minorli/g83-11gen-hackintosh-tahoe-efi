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

## 2. 已正常功能

- USB、电池状态、内置键盘
- 触摸板（I2C HID）+ 触摸板手势（VoodooRMI/VoodooI2C）
- 内屏 eDP 显示、开局亮度、`Fn` 亮度键
- GPU 加速（Metal）
- **外接 HDMI 4K@60Hz 稳定输出**（黑闪已根治）✅
- 声卡：扬声器 / 耳机 / 内置麦克风（`alcid=11`）
- Wi-Fi（AirportItlwm）/ 蓝牙（Intel）/ 有线网卡（IntelMausi）

## 3. 已知问题

- **系统睡眠已禁用**（`sleep 0`）：S3 挂起在 Intel WiFi / 触控板 I2C / 本驱动的组合下目前不可用，请勿重新打开系统睡眠；熄屏（显示器关闭后）唤醒已通过 `-wex-dcoff` 解决。
- Wi-Fi 延迟偶发波动（5GHz 高带宽信道下 ping 波动大），与显示驱动无关。
- Realtek 读卡器未驱动（kext 已禁用）。

## 4. 使用说明

1. 用磁盘工具（或 `diskutil mount diskXs1`）挂载 EFI 分区，把本仓库的 `EFI` 覆盖过去（机器自带的 Windows 引导目录可保留不动）。
2. BIOS 设置：关闭 **Secure Boot**即可启动（其他项保持默认）。
3. **三码替换（必做一次）**：`config.plist` 的 `PlatformInfo→Generic` 中 `SystemSerialNumber / MLB / SystemUUID / ROM` 均为空——请用 [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) 选择 `MacBookPro16,2` 生成后再填入；`ROM` 填 12 位随机 MAC，或从另一台真 Mac 抓取（iMessage 更容易通）。**请勿直接使用网络/他人已发布的 serial**。
4. BOOT-ARGS 已按最小集配置，一般无需添加；如遇排障可临时加上 `-wex-hdmi-diag` 等诊断参数（见 [WhateverXe-Dynabook 仓库](https://github.com/xiananliu/WhateverXe-Dynabook) 的 README）。
