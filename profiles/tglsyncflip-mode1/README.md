# G83 本机事务同步配置

维护者在2026-11-16观察到TGLSyncFlip模式1消除了原先UU引发的Dock下部／画面下沿闪烁，
明确要求停止观察并固化。本设备活动EFI已仅追加`tglsyncmode=1`，当前运行模式1。
**下次启动默认值已写好，实际启动仍待维护者手动核验；RCS／CCS卡死未证明修复。**

此目录保存与本机相同SHA的独立插件、许可和`profile.json`配置片段，不含机器身份或完整EFI。
代码固定于WhateverXe-Next `24dcd3254ef98c1efa0ce31db60c80fb1929e662`；
审查结论固定于该仓库`a5062dd`。本轮没有修改或重新编译插件源码。

## 已测试的组件组合

本机仍使用下游WEX 0.8.1 DEBUG（内部版本0.8.0）、Lilu 1.7.2，以及现用Graphics 0.4.3 Metal；
VA保持隔离。完整组件SHA和源码摘要在`profile.json`，插件二进制SHA为
`3d772787d294c4ed29a516de3ba66ee7a3da603f28c9b4f2a8f674c2bbfab5ec`。
插件只能在framebuffer符号／偏移和21处字节锚点符合时生效，保持原拒绝逻辑。

初次固化时仓库根模板仍为WEX0.8.2。后续根`EFI/`已按维护者要求对齐本机受测组合，
包含同SHA插件和模式1参数，见[当前EFI](../../docs/Current-EFI.md)。0.8.2候选留在历史中，
未整机部署；公开配置身份已清空，不能直接覆盖本机私人config。

## 配置语义

1. 将本目录的`EFI/OC/Kexts/TGLSyncFlip.kext`放入目标EFI；
   在现有Lilu和WhateverXe后面加入`profile.json`列出的Kernel/Add条目，避免重复。
2. 保留完整原boot-args，仅确保含`-tglsync tglsyncmode=1`；**不能用这两个参数替换原字符串**。
   本机已含`-tglsync`，此次唯一语义变化是追加`tglsyncmode=1`。
3. 本机NVRAM/Delete已含boot-args，无需直接写NVRAM。用匹配的ocvalidate 1.0.8核验后，
   由维护者自行决定启动时机。此片段不是自动部署脚本。

下次启动后，在相邻WhateverXe-Next源码目录运行：

```sh
sudo -n probe/TGLSyncFlip/build/tglsync-ctl status
```

应为`state=routed armed=1 mode=1(transaction)`。若非routed，记录实际状态，不把参数存在当作生效。

## 回退

临时回退：同目录执行`sudo -n probe/TGLSyncFlip/build/tglsync-ctl mode 0`，
再用sudo刷新status核对；这不会撤销下次启动默认1。
持久回退：删除`tglsyncmode=1`或改为`tglsyncmode=0`。完全禁用可追加`-tglsyncoff`，
或禁用Kernel/Add中的插件条目。

若本机无法启动，使用已进入过的UEFI Shell，`map -r`后逐个`fsN:`查找
`\EFI\OC\config.plist.pre-sync-default1-20261116-141953`，在正确文件系统执行：

```text
cp \EFI\OC\config.plist.pre-sync-default1-20261116-141953 \EFI\OC\config.plist
```

确认复制成功，`exit`，由维护者手动启动；不Reset NVRAM。这份备份恢复默认0并保留插件。
完整撤销插件安装的更早备份为`config.plist.pre-tglsync-20261116-102`。

## 交给审查者

先看[WhateverXe-Next固化聚合](https://github.com/Minorli/WhateverXe-Next/blob/a5062dd/validation/syncflip-persist-mode1-20261116.json)
和[源码及审查](https://github.com/Minorli/WhateverXe-Next/blob/a5062dd/docs/REVIEW.md)。
13:57:01–14:18:16的1274.7秒窗口没有恢复调用或新增GPU报告；负载未固定，Windows App进程也存在。
RCS／CCS状态事件有活动，但不是逐客户端完成stamp，不能证明此前的挂起已修复。
本轮不新增精确闪烁0次或Poisson显著性结论；症状逐次计数仍未知。

请分别审查原生事务参数恢复、完成通知／返回值契约、并发模式门控，以及RCS／CCS历史事故。
同步改变呈现节奏，不修改GPU复位、POSH或完成stamp；不解除既有POSH／Metal风险。
