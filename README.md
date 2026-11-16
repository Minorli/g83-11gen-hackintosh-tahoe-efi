# Dynabook G83 · macOS

面向 **Intel Core i7-1165G7 / Iris Xe 96EU** 实机持续维护的 OpenCore EFI。
本仓库的 `main` 是该机器的主迭代线路：记录硬件、保存可追溯配置、逐项验证功能，
将通用修复独立回送上游。

**当前处于开发与稳定性修复阶段。** 本机事务同步模式1消除了此前观察到的UU闪屏，
已按维护者要求写入启动配置；RCS／CCS队列卡死仍未证明修复。
官方 Graphics 0.4.3 Metal 已明显抑制本机 WindowServer 内存增长，但长期验收仍未完成。
不能将“可以启动、Metal 测试通过”视为日常稳定性验收完成。

## 目标硬件

| 项目 | 本机检测结果 |
| --- | --- |
| 系列 | Dynabook G83，11 代 Tiger Lake；具体机身子型号待核对铭牌 |
| CPU | Intel Core i7-1165G7，4 核 / 8 线程 |
| 核显 | Intel Iris Xe，96 EU，物理 `8086:9A49` revision `01` |
| 内存 | 24 GB |
| 无线 | Intel Wi-Fi 6 AX201，`8086:A0F0` |
| 声卡 | Realtek ALC257 |
| 存储 | NVMe，当前协商 PCIe 3.0 ×4；TRIM 已启用 |
| 引导 | OpenCore 1.0.8 RELEASE |
| 当前实机系统 | macOS Tahoe 26.7.3（25G342），Darwin 25.6.0 |
| SMBIOS | MacBookPro16,2；这是兼容身份，不是物理机型号 |

仓库保留了按内核版本分流的 Sequoia / Tahoe 组件，但本机这一轮仅验证 Tahoe。
原方案在其他 CPU / 系统上的结果不作为本机验收结论。CPU 的 CPUID 兼容设置与
物理型号是两回事；不会为了显示 i7 而随意改动启动所需的 CPUID 仿真。

## 功能状态

| 功能 | 当前状态与边界 |
| --- | --- |
| 启动、桌面与基础 Metal | 已在本机使用；不代表图形栈稳定 |
| 图形内存 | 本机已加载官方 0.4.3 Metal，增长明显改善；异步生命周期和长期稳定性未完成验收 |
| 远程画面与稳定性 | 本机TGLSyncFlip模式1后未再观察到原UU闪屏，已持久配置；实际下次启动默认值待核验 |
| GPU 队列 | 历史BCS及近期RCS→CCS挂起／恢复循环仍单独调查；同步呈现未证明修复卡死 |
| H.264 / HEVC 硬解 | 未验收；曾发生 VA 路径崩溃，本机 VA bundle 目前隔离 |
| Wi-Fi | AX201 企业认证修订已在本机连接成功；吞吐和长期漫游尚未验收，见[无线说明](docs/AirportItlwm/README.md) |
| 蓝牙 | 控制器与 USB 枚举正常；耳机、AirDrop、睡眠恢复需分别验证 |
| USB | 主 XHCI 表含 16 个逻辑端口，实机只创建 15 个；SS04 未出现，待逐口重新映射 |
| 音频与麦克风 | 驱动和输入设备已注册；每路录音、耳机切换、长时间使用仍需测试 |
| 键盘与触控板 | 已使用；I2C 设备实际运行在 APIC 中断模式 |
| 亮度与显示布局 | 保留当前工作配置及有意设置的双屏镜像；未声称外接 4K / 所有模式都已验收 |
| 摄像头 | USB 设备已枚举；视频通话验收待完成 |
| 电池 | 系统可报告状态；健康度读数需与固件或其他系统交叉核对 |
| TF（microSD） | 本机 RTS522A（`10ec:522a`）已加载并绑定 RealtekCardReader 0.9.7；Tahoe 插卡、读写与弹出待验收 |
| 系统睡眠 | 已收录 S0ixStandby 0.5.4 和 Deep Idle SSDT，默认禁用；本机尚未验证，HDMI 唤醒与接电 dark wake 有上游限制 |

## 2026-11-16 上游集成

已合入 G83 上游 `ed57bf0`（EFI 1.4.6）的组件更新，维护线版本为 `1.4.6-minor.1`。
模板使用下游 WhateverXe 0.8.2，保留 HDMI / CDCLK / 背光增强；旧 VA 回退、Metal 泄漏、
eDP 训练等实验补丁已被下游移除。**仓库模板更新不等于本机已部署。**

S0ixStandby 与 SSDT 已收录，仍保持 `Enabled=false`，CPUID 暂保留 Coffee Lake 兼容值；
睡眠候选需成组切换 Ice Lake CPUID、SSDT 与 kext，不能只启用其中一项。
本机继续使用既有 WEX / Metal，并保留 VA 隔离。新的 Graphics 0.4.4 包没有新的 Metal 或
framebuffer 代码，且存在视频合成转换回归报告；不通过直接运行安装包来覆盖当前工作系统。
完整组件比对、部署分组和已知限制见[本轮集成记录](docs/Integration-20261116.md)。

本机已单独固化TGLSyncFlip 1.0.2事务同步模式1。相同SHA插件、配置片段和回退见
[本机呈现配置](profiles/tglsyncflip-mode1/README.md)；公开模板仍为WEX 0.8.2，
本机受测组合仍用原下游0.8.1，因此未将两套配置的验证结论混用。

## 使用与维护

1. 先备份已知可启动 EFI，并准备能实际进入的恢复入口。不同 G83 子型号的 ACPI、
   USB 表和显示链路不能直接套用。
2. 公开 `config.plist` 的身份字段保留为空值或全零占位。为自己的机器生成独立
   `SystemSerialNumber`、`MLB`、`SystemUUID` 与 `ROM`；升级时保留本机身份。
3. 按 [OpenCore 配置校验说明](docs/Config-validation.md) 使用匹配版本的 `ocvalidate`。
   格式校验通过不能证明 ACPI、USB 映射、电源管理和图形稳定性正确。
4. 显卡依赖 EFI 中的内核组件及系统中的图形运行组件。来源和版本边界见
   [组件来源](docs/Provenance.md)。当前仓库没有自动安装或自动恢复被隔离 VA 的步骤。
5. 一次只部署一个可回退的变更，记录源码版本、二进制 SHA-256、启动版本和实测结果。
   不直接用公开配置覆盖正在工作的私人 EFI。

本机不运行会覆盖现用图形组件的 OCLP 自动修补任务。系统睡眠保持当前策略；
显示熄屏、系统睡眠和休眠需要分别验收。配置更新不会自动修改这些系统级设置。

## 迭代方向

优先解决图形内存增长、远程呈现闪屏和 BCS 卡死，再完成 USB 实机映射、
固定负载下的功耗对照、音频 / 蓝牙与睡眠验收。完整巡检见[维护路线](docs/Maintenance.md)。

独立显卡项目为 [WhateverXe-Next](https://github.com/Minorli/WhateverXe-Next)，
集中维护可审查源码、诊断工具与脱敏验证材料；本仓库负责 G83 的整机集成。
验证充分的通用修复使用单独分支提交上游。无线贡献分支与整机维护分开保存。

开发分支推送只执行配置校验。EFI 打包发布需手动触发，并标记为预发布；
当前没有“完美 EFI”或图形完全稳定的发布承诺。

## 来源与许可

本仓库继承 BSD-3-Clause 许可，见 [LICENSE](LICENSE)。组件版权及各自许可证保持原样，
完整来源见 [Provenance](docs/Provenance.md)；固件等第三方资源适用其单独许可。
