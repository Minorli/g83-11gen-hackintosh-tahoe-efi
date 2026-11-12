# 组件来源与集成边界

本维护线基于 [G83 EFI 上游](https://github.com/xiananliu/g83-11gen-hackintosh-tahoe-efi)
提交 `112eca2dc2600dfacec427f91ac2648cf0025457`，保留原 Git 历史及许可证。
本机维护、功能验收和发布由本 fork 独立推进，不继承原机的全部兼容结论。

| 组件 | 来源与说明 |
| --- | --- |
| OpenCore | [acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)，1.0.8 RELEASE |
| Lilu / VirtualSMC / AppleALC | [Acidanthera](https://github.com/acidanthera)，各组件保留自身许可 |
| WhateverXe | [b00t0x/WhateverXe](https://github.com/b00t0x/WhateverXe)，BSD-3-Clause |
| 当前 WEX 下游二进制 | [wex-modified-edition](https://github.com/xiananliu/wex-modified-edition)，release 0.8.1，内部版本 0.8.0；对应下游修改源码未公开 |
| 图形运行组件 | [WhateverXe-Graphics](https://github.com/b00t0x/WhateverXe-Graphics)，当前对照 v0.4.1；系统安装状态不包含在本 EFI 仓库中 |
| 独立显卡研究 | [WhateverXe-Next](https://github.com/Minorli/WhateverXe-Next)，源码审查、BCS 诊断与 POSH 生命周期研究；不代表当前 EFI 已加载其构建 |
| AirportItlwm | [OpenIntelWireless/itlwm](https://github.com/OpenIntelWireless/itlwm)；本地认证修订、源码和许可证见 [AirportItlwm](AirportItlwm/README.md) |
| Intel 蓝牙 / 有线网络 | [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) / [IntelMausi](https://github.com/acidanthera/IntelMausi) |
| Tahoe 音频 | [SimpleHDA](https://github.com/laobamac/SimpleHDA) |
| 数字麦克风 | [TGLDmic](https://github.com/labrouss/TGLDmic)，BSD-3-Clause；[HAL 启动时序集成](https://github.com/xiananliu/TGLDmic-HAL-Fix)；固件见 [LICENCE.Intel](TGLDmic/LICENCE.Intel) |
| 输入 | [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C)、[VoodooRMI](https://github.com/1Revenger1/VoodooRMI)、[VoodooPS2](https://github.com/acidanthera/VoodooPS2) |
| USB | [USBToolBox](https://github.com/USBToolBox/kext)，UTBMap 为继承的机器映射，需按本机复核 |
| 亮度快捷键 | [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys) |
| SMBIOS 身份工具 | [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)，自行生成唯一身份 |

下游 README 的用户空间修复（含泄漏、VA 和部分 eDP 修订）限定
物理 `8086:9A49 rev1` + **Sequoia**。Tahoe 配置中存在同名参数不等于修复生效。
上游源码重新编译也不会自动包含未公开的亮度、HDMI 等下游改动。

`config.plist` 的 CPU 兼容仿真、SMBIOS 与物理 CPU 信息应分开理解。
更新硬件说明不构成删除兼容仿真或改写设备 ID 的理由。
