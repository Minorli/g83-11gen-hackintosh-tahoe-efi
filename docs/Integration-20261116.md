# 2026-11-16：G83 上游、显示审查与分阶段部署

本轮目标是吸收可追溯更新，准备本机候选；不是给未启动的配置补写“稳定”结论。
主机为 i7-1165G7 / Iris Xe 96EU / 24GB DDR4 / Tahoe 26.7.3（25G342）。
审查与候选暂存阶段没有修改活动系统。随后经维护者明确批准，仅将 TF-only 配置写入活动 EFI，
待维护者手动重启验证；系统 Library、VA 隔离及镜像布局均未改变。

## 核对结论

| 项目 | 结论 / 证据层级 | 集成决定 |
| --- | --- | --- |
| WhateverXe 官方更新 | **成立〔Git 源码〕**：最新 release 为 0.8.2。已集成源码基线 `4512191b` 至当前官方 main `2bad7171` 仅变更 CHANGELOG.md，没有遗漏新内核修复 | 保留本地修复，合并最新说明；不把文档更新称作新代码修复 |
| G83 上游 1.4.6 | **成立〔Git 差异〕**：合入 `ed57bf0`，保留本维护线企业认证、I2C 顺序、配置校验及私有身份隔离 | 模板版本 `1.4.6-minor.1`；发布仍需手动触发且为 prerelease |
| 移除私有 GPU 补丁 | **部分成立〔下游 release / 二进制〕**：0.8.2 移除旧 VA、MTL、eDP 训练、VBT 实验与诊断；仍保留 HDMI clock600/SCDC、CDCLK、背光增强 | 不是纯上游 WEX；不以“移除补丁”推导闪屏已修复 |
| TF 读卡器 | **成立〔本机 PCI / 静态依赖 / 落盘核验〕**：RTS522A `10ec:522a` 匹配 0.9.7；kmutil 所列符号均可解析 | 经批准已写入 TF-only 配置，只启用已有 RealtekCardReader；Friend 保持禁用。重启加载与插卡仍待本机验收 |
| 睡眠修复可直接照搬 | **不成立〔来源范围 / 源码〕**：上游 G83 验证是 Sequoia 15.8.2；S0ix 项目还报告 HDMI 带线唤醒冻结与接电 dark-wake 再睡眠冻结 | S0ixStandby、SSDT 收录但默认关闭，CPUID 保留既有值；不自动改 pmset |
| Graphics 0.4.4 能修本机 UU 闪屏 | **证据不足〔静态比对〕**：其 Metal 与 framebuffer 和现用文件完整 SHA 相同；发行说明的新增项是 H.264 编码回归修复 | 不把更新包当闪屏修复；单独准备无 VA 的图形更新候选 |
| Claude 审查 | **成立〔材料核验〕**：显示审查 §7.6 支持继续检验 async 事务，并提供独立 SyncFlip 候选 | 保留现用 WEX 进行同启动对照，先解决候选的回退语义问题 |
| DeepSeek 独立审查已归档 | **证据不足〔材料核验〕**：维护者提供的 3 个链接为 review README、提示词、plane-trace 证据；未包含独立完成的 findings | 不虚构其结论或把输入材料算作第二份认可；不阻塞已能独立核对的集成 |

## 精确文件比对

完整机器可识别信息、Apple 文件、反汇编和原始寄存器只留本机。
公开脱敏清单同步见 WhateverXe-Next 的 `validation/integration-20261116.json`。

| 组件 | SHA-256 | 状态 |
| --- | --- | --- |
| 现用下游 WEX（0.8.1 DEBUG，内部 0.8.0） | `df96a2cac5c68fb5a1540045da53b9871729561bba1c4714e03ae700466f4ed3` | 本机已加载；保留 TGLProgram 记录器 |
| 新下游 WEX 0.8.2 RELEASE | `fbd0495dde8242fe11ed13d7bd2b62d6d4407eb58201259105818f5d2b5d1e79` | 模板已合入，本机未安装；失去 plane-trace 入口 |
| 现用及新包 framebuffer | `115a4616e862228a8c82df6e52ba41ac02b9750f116352ff3575aadcc950888e` | 完全相同 |
| 现用 0.4.3 及新包 0.4.4 Metal | `25b6c72ee23fb71f91384982fb591d6d1e514c73be3fea99831b4559d0ec4faa` | 完全相同 |
| 新包 accelerator | `6c8ab6cce50b9529679ee4fdf5b1c37d091677d945076f41cb4b7f8196c51449` | __TEXT / __DATA 与现用一致，__LINKEDIT 不同；不放宽现有完整 SHA 守卫 |
| 新包 Compat | `796e041f7a923706bf5ea47933297d8a72ea22aeb6535809cba099727dc6232c` | 代码与数据有变化；不是只换包装 |
| Graphics 0.4.4 pkg | `f4c2d680a7c52c33a3f038ce6945da0aa2cc044e62b935a52ea046c7c473cfec` | 匹配发行 SHA256SUMS；仅解包 |
| S0ixStandby 0.5.4 | `6eba0d32066ab0fed8ed7e2c20d2a5d65d2c4dd01b9523a7aa1f0232bf60991b` | 随 G83 上游保留原 BSD 许可；未启用 |
| RealtekCardReader 0.9.7 | `9788705dbe8c812924956e5c82591d17d57d327f4b018701c20ab2aff00cfa64` | 文件本来就在本机 EFI；候选仅改 Enabled |

新包 GL、编译器和 Metal 辅助 dylib 也与本机相同。新 Compat / accelerator / Metal / VA
完整 bundle 的 `codesign --verify --deep --strict` 均通过；签名通过不代表功能通过。

官方安装包会同时恢复 VA、写 CoreMedia FeatureFlag 并重建 AuxKC。
因此本轮没有运行 installer。私有图形暂存排除了 VA；当前 AllowOnePassMetalScaling 已为 false。
[#129](https://github.com/b00t0x/WhateverXe/issues/129) 中 loverlf 报告 Sequoia / 9A49 合成转换使
WindowServer abort，vikenzo196 又报告 Tahoe 26.7.3 / RPL 的 AirPlay 转换同类故障。
其分析将问题指向 VA VP HAL 的拒绝路径，不能把它简化为 2560×1440 分辨率限制；本轮未独立复现。
保留 VA 隔离会避开该特定已报告入口，但不能证明其他路径稳定。

## 已准备的实际机器候选

候选从**实际活动 EFI 的完整备份**生成，未用公开空身份 config 覆盖机器。
本机权限 700 的 `g83-machine-local/集成候选-20261116` 保存文件、差异、回退与私有 SHA。
公共模板与两份实际候选均通过 OpenCore 1.0.8 的 ocvalidate。

1. **TF-only**：唯一差异为 Kernel/Add 中 RealtekCardReader 的 Enabled=false→true。
   WEX、无线、CPUID、ACPI、USB 表、身份和全部 boot-args 不变。重启后插卡，检查识别、挂载、
   文件读回和正常弹出；先读已有数据，写入仅用可丢弃测试卡并确认后进行。
   回退为恢复这一项 false 或恢复完整 baseline config，下一次手动启动生效。
2. **图形 0.8.2 / 0.4.4 无 VA 候选**：分开存放整套 EFI 与所需 Library 文件；不含 VA。
   保留本机必要参数（含既有 dcoff、音频、TSC 和诊断设置），仅移除 4 个已失去实现的 VA / leak / eDP 参数。
   不改变 TF 状态、CPUID、ACPI、身份、USB 与企业 Wi-Fi。它会改变多个图形组件，只能作为版本更新验收，
   不能拿它与旧版本的差异证明 async / POSH 因果。需先完成 SyncFlip 采集，再考虑安装。
   安装前还须备份系统相关 bundle 与 Auxiliary KC；EFI Shell 只恢复 EFI 不足以撤销 Library / KC 变化，
   必须另有能挂载系统卷的 Recovery 入口并写明完整回退动作，本轮未准入安装。
3. **睡眠资产**：只暂存 kext 和 SSDT，未生成可直接引导的睡眠 profile。
   先取得固件 DSDT/SSDT 并排除 `_SB.LPS0`、`_GPE.LXEN` 重名；ioreg 中没有名字不能代替方法级核对。
   本机存在 PNP0D80/PEPD 设备；CPUID / SSDT / kext 必须成组启用。
   另行确认保存工作、恢复入口、HDMI 断开及电池首测条件后，再做手动短睡眠；
   不先启用合盖或自动睡眠，不将硬件 SLP_S0=0 的 Deep Idle 称为完整低功耗 S0ix。
4. **UU 闪屏候选**：WhateverXe-Next 的独立 TGLSyncFlip 本机已构建，11 个锚点匹配，CPU 4 组通过，
   本轮已修正旧10.6 ABI并增加真实SDK vtable回归测试；298个非Lilu导入均解析，9个Lilu导入由启动时Lilu提供。
   **未安装**。原模式2会持久改 plane CTL 缓存；native configurePlane 在
   `0x21CF` 参数相同可直接走 `0x22A0` 快路径，因此切回0不能无条件保证缓存/硬件立即恢复 async。
   在修正恢复语义、补回归测试并通过原生 trace 验证前，不开始 0/1/2 盲测。
   模式2原生轮询还可能超时，不能承诺“总是等真实锁存后才完成”。

## 部署与回退入口

维护者已说明：没有独立启动验证的备用 EFI，仅有曾进入过的 UEFI Shell。
原 SyncFlip 安装任务明确要求备用 EFI，因此安装门槛尚未满足；本轮没有反复询问相同事实。
维护者随后明确批准 TF-only 安装及同分区 Shell 回退，授权范围只覆盖这一轮。
该授权不替代 SyncFlip 的安装门槛；其他候选继续保留各自的恢复条件。任何重启时机仍由维护者决定。

**成立〔落盘核验，不是启动验收〕**：活动 EFI 的唯一既有文件变化为 `OC/config.plist`，
唯一语义变化是 `/Kernel/Add/26/Enabled` 将 RealtekCardReader 从 false 改为 true。
新增的 `OC/config.plist.pre-tf-20261116` 与安装前 baseline 逐字节相同；临时配置已移除。
全部其他 EFI 文件、WEX、企业无线、身份、CPUID、ACPI、USB 与 boot-args 均保持原样。
安装后活动配置通过 ocvalidate 1.0.8；尚未重启，不能声称读卡器已加载或 TF 问题已修复。

若本轮不能启动，从已使用过的 OpenCore `UEFI Shell (Recovery)` 进入，执行 `map -r`，
逐个 `fsN:` 查找 `\EFI\OC\config.plist.pre-tf-20261116`，不要假定 `fs0:`。
在找到该文件的文件系统执行：

```text
cp \EFI\OC\config.plist.pre-tf-20261116 \EFI\OC\config.plist
```

确认复制成功后 `exit`，由维护者手动重新启动，不需 Reset NVRAM。
Graphics Library / KC 更改与 EFI-only 回退分开处理，不能删除 boot-arg 就算恢复所有组件。

## 来源

- [G83 上游 ed57bf0](https://github.com/xiananliu/g83-11gen-hackintosh-tahoe-efi/commit/ed57bf051d97578ce8c1afb8e6f2338335be2de1)，1.4.0/1.4.5/1.4.6 说明保留于 docs/releases；其中“本机”指上游维护者的机器。
- [下游 WEX 0.8.2](https://github.com/xiananliu/wex-modified-edition/releases/tag/0.8.2)。
- [官方 WhateverXe 0.8.2](https://github.com/b00t0x/WhateverXe/releases/tag/0.8.2) 与 [Graphics 0.4.4](https://github.com/b00t0x/WhateverXe-Graphics/releases/tag/v0.4.4)。
- [S0ixStandby v0.5.4](https://github.com/labrouss/S0ixStandby/tree/c52f172b934c0f906fccb74ad2bce0a974ca5bb1) / [HDMI 唤醒报告 #3](https://github.com/labrouss/S0ixStandby/issues/3)。
- [Claude 显示审查 §7.6](https://github.com/Minorli/WhateverXe-Next/blob/eeace38/docs/ClaudeReview-20261113-Display.md)。
