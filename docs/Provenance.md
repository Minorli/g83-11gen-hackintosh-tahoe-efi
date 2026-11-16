# 组件来源与集成边界

本维护线基于 [G83 EFI 上游](https://github.com/xiananliu/g83-11gen-hackintosh-tahoe-efi)
初始提交 `112eca2dc2600dfacec427f91ac2648cf0025457`，2026-11-16 合入至 `ed57bf051d97578ce8c1afb8e6f2338335be2de1`，保留原 Git 历史及许可证。
本机维护、功能验收和发布由本 fork 独立推进，不继承原机的全部兼容结论。

| 组件 | 来源与说明 |
| --- | --- |
| OpenCore | [acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)，1.0.8 RELEASE |
| Lilu / VirtualSMC / AppleALC | [Acidanthera](https://github.com/acidanthera)，各组件保留自身许可 |
| WhateverXe | [b00t0x/WhateverXe](https://github.com/b00t0x/WhateverXe)，BSD-3-Clause |
| 当前 WEX 下游二进制 | [wex-modified-edition](https://github.com/xiananliu/wex-modified-edition)，模板为 release 0.8.2 RELEASE，内部版本 0.8.2；本机活动 EFI 仍为原 0.8.1 DEBUG（内部 0.8.0）；对应下游修改源码未公开 |
| 图形运行组件 | [WhateverXe-Graphics](https://github.com/b00t0x/WhateverXe-Graphics)，本机为 v0.4.3 Metal，v0.4.4 安装包已解包比对但未安装；系统安装状态不包含在本 EFI 仓库中 |
| 独立显卡研究 | [WhateverXe-Next](https://github.com/Minorli/WhateverXe-Next)，源码审查、BCS 诊断与 POSH 生命周期研究；其内核恢复候选未部署，已部署独立插件单列如下 |
| 本机事务同步插件 | `profiles/tglsyncflip-mode1`包含TGLSyncFlip 1.0.2同SHA二进制，代码固定于WhateverXe-Next `24dcd325`，BSD-3-Clause许可随附；本机已加载，原生恢复候选未部署 |
| AirportItlwm | [OpenIntelWireless/itlwm](https://github.com/OpenIntelWireless/itlwm)；本地认证修订、源码和许可证见 [AirportItlwm](AirportItlwm/README.md) |
| Intel 蓝牙 / 有线网络 | [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) / [IntelMausi](https://github.com/acidanthera/IntelMausi) |
| Tahoe 音频 | [SimpleHDA](https://github.com/laobamac/SimpleHDA) |
| 数字麦克风 | [TGLDmic](https://github.com/labrouss/TGLDmic)，BSD-3-Clause；[HAL 启动时序集成](https://github.com/xiananliu/TGLDmic-HAL-Fix)；固件见 [LICENCE.Intel](TGLDmic/LICENCE.Intel) |
| 输入 | [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C)、[VoodooRMI](https://github.com/1Revenger1/VoodooRMI)、[VoodooPS2](https://github.com/acidanthera/VoodooPS2) |
| USB | [USBToolBox](https://github.com/USBToolBox/kext)，UTBMap 为继承的机器映射，需按本机复核 |
| Deep Idle 睡眠候选 | [labrouss/S0ixStandby](https://github.com/labrouss/S0ixStandby)，v0.5.4 / `c52f172b`；BSD-3-Clause，原许可随 kext 保留；本维护线默认禁用，未继承其他机器的睡眠验收 |
| TF 读卡器 | [0xFireWolf/RealtekCardReader](https://github.com/0xFireWolf/RealtekCardReader)，0.9.7；沿用 G83 上游所带文件，启用不等于本机功能已测 |
| 亮度快捷键 | [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys) |
| SMBIOS 身份工具 | [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)，自行生成唯一身份 |

旧 0.8.1 下游 README 的用户空间修复（含泄漏、VA 和部分 eDP 修订）限定
物理 `8086:9A49 rev1` + **Sequoia**。Tahoe 配置中存在同名参数不等于修复生效。
上游源码重新编译也不会自动包含未公开的亮度、HDMI 等下游改动。

`config.plist` 的 CPU 兼容仿真、SMBIOS 与物理 CPU 信息应分开理解。
更新硬件说明不构成删除兼容仿真或改写设备 ID 的理由。

0.8.2 下游已移除上述私有 GPU / VA / eDP 实验补丁与诊断记录器，要求配合官方 Graphics 0.4.4。
保留 HDMI 时钟 / SCDC、CDCLK 与背光增强，不是退回纯上游二进制。见[本轮集成](Integration-20261116.md)。
