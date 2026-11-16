# 2026-11-16 本机 EFI 快照

根 `EFI/` 已从活动 EFI 的只读挂载中同步，替代此前未部署的 WEX 0.8.2 模板。
这是本机当前使用组合的保存点。原集成历史、上游1.4.6更新和未部署候选仍保留，
不是将所有候选标为已通过。

## 组成与边界

| 项目 | 当前快照 |
| --- | --- |
| OpenCore / Lilu | 1.0.8 / 1.7.2，文件按实机保留 |
| WhateverXe | 下游0.8.1 DEBUG，内部0.8.0；SHA `df96a2ca…f4ed3` |
| TGLSyncFlip | 1.0.2，SHA `3d772787…ab5ec`；保留 `-tglsync tglsyncmode=1` |
| TGLDisplayProbe | 1.0.1，SHA `192e72cf…ecf63`；原生RAM计数版，无MMIO写入 |
| AX201 企业认证 | 保留本机已认证成功的 Tahoe 修订，SHA `beccc746…54f0d` |
| TF | RealtekCardReader已启用；驱动绑定已确认，插卡/读写仍待验收 |
| 睡眠候选 | 不混入活动快照，存于 `profiles/s0ix-candidate/`，未部署 |
| 系统图形 | 现用0.4.3 Metal和VA隔离状态只记录指纹；不分发Apple系统文件 |

完整路径和SHA见 [Current-EFI-manifest.json](Current-EFI-manifest.json)。
事务同步模式1在当前启动中已运行；**写入默认1后的下一次启动仍待核验**。
维护者观察支持原UU闪屏改善，没有精确全窗计数；RCS/CCS卡死仍未证明修复。
本快照保留实际参数，包括旧参数；不顺便改动显示、VA、睡眠或恢复策略。

## 同步校验

- 167个公开EFI文件；除config外全部文件内容与活动EFI一致。
- config只清空MLB、SystemSerialNumber、SystemUUID并将ROM置为6字节零。
  将这四项还原后，与私人config解析结果完全相同。
- 两仓库的公开config相同，均通过ocvalidate1.0.8；44个启用的ACPI、kext、driver、tool
  条目都能解析到文件。FAT不提供有意义的Unix执行位，仓库保留原跟踪文件的权限。
- 同步前后活动EFI整树SHA清单相同；本轮未写活动分区、未安装或重启。
- 私人config、NVRAM、原始诊断、身份和私人恢复配置不进入仓库；完整原始备份仅在本机。

## 使用与回退

公开config的身份为空，不能直接覆盖本机私人config。部署应先读取
[维护流程](Maintenance.md)，只合并实际获准的差异并保留本机身份。
本机既有UEFI Shell可用于查找和恢复同分区config备份；尚无已验证的独立备用EFI启动介质。

恢复同步模式或撤销插件的具体方法见[模式1配置](../profiles/tglsyncflip-mode1/README.md)。
本次仓库同步没有改变本机，因而不需要在实机执行回退。此前0.8.2候选可从历史提交
`d89fdc1a390f5da92469a3f4b820f85efc0e1e93`查看，仍不等于整机已测。

WhateverXe-Next 的 `deployments/g83-current/` 保存相同脱敏配置、指纹和本仓库固定提交；
完整EFI以本仓库为准。两个插件的BSD-3-Clause许可见各自来源文档及随附许可。
