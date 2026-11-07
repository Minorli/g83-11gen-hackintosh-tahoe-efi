# g83-11gen-hackintosh-tahoe-efi

G83(第 11 代 Intel Tiger Lake 笔记本)黑苹果 EFI 公开配置,目标系统 **macOS Tahoe (26)**。先私有维护,确认稳定后再转公开。

## 系统信息

| 项目 | 值 |
|------|-----|
| 机型 | G83(11th Gen Tiger Lake) |
| 核显 | Intel Iris Xe (`0x9a49`),WhateverXe 注入驱动 |
| SMBIOS | MacBookPro16,2 |
| 目标系统 | macOS Tahoe 26.x(内核 25.6.x) |
| OpenCore | 1.0.8 **RELEASE**(对应调试期构建 `DBG-108-2026-09-27`) |

## 目录结构

```
EFI/
├── BOOT/
│   └── BOOTx64.efi        # OpenCore bootstrap
└── OC/
    ├── ACPI/              # SSDT(亮度、EC/USBX、I2C、PLUG、PNLF 等)
    ├── Drivers/           # HfsPlus / OpenCanopy / OpenRuntime / ResetNvramEntry
    ├── Kexts/             # 见下
    ├── Resources/         # OpenCanopy 主题资源
    ├── config.plist
    └── OpenCore.efi
```

本配置**不含任何 Windows 引导项 / Microsoft 目录**,仓库里可直接整个 `EFI` 覆盖使用(需要 UUID/序列号已按你机器修改)。

## 关键 Kext 说明

- **AirportItlwm** —— Intel 无线网卡原生 Wi-Fi 驱动(替换 itlwm + HeliPort 方案)
- **IntelBluetoothFirmware + BlueToolFixup + IntelBTPatcher** —— Intel 蓝牙
- **WhateverXe / Lilu** —— Tiger Lake 核显加速
- **USBToolBox + UTBMap** —— USB 端口定制
- **VoodooI2C/HID/RMI + PS2Controller** —— 触摸板 / 键盘
- **RealtekCardReader + Friend** —— Realtek 读卡器
- **IntelMausi / SimpleHDA + AppleALC** —— 网卡 / 声卡

## ⚠️ 注意

1. config.plist 已抹除三码:`SystemSerialNumber` / `MLB` / `SystemUUID` / `ROM` 均为空。使用前请用 **GenSMBIOS** 按本机重新生成,勿直接使用。
2. 发布版配置:OpenCore 1.0.8 **RELEASE**、`boot-args` 精简为最小集、`Misc→Debug` 全部关闭(Target=0,不再向硬盘写 opencore 日志)。
3. 调试期曾使用 `debug=0x100 watchdog=0 -wex-hdmi-diag alcverbs=1`,均已移除;驱动机器时如需排障可临时加回。
4. `oc_stable` 来源于本人开机在用的稳定目录;Windows 引导项(`EFI/Microsoft`)与调试日志不包含在本仓库。

## 开机验证

- Wi-Fi / 蓝牙 ✅(AirportItlwm,5GHz 802.11ac)
- 核显加速 ✅(Metal 2,30bit 输出)
- 音频 / 电池 / 亮度 ✅
