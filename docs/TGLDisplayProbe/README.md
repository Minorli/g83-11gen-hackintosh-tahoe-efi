# TGLDisplayProbe 1.0.1 来源

根EFI所带二进制来自Minorli/WhateverXe-Next的原生RAM-only探针，源码提交
`45eb1cb3cabbfc546ae7b61f985fa0db85fce608`，主文件SHA为
`192e72cf5b53da0b09d7b606177470a1a1cb94b9fb002fa5e596d9892b2ecf63`。
源文件声明`SPDX-License-Identifier: BSD-3-Clause`；许可全文随附于[LICENSE](LICENSE)。
本轮从活动EFI复制现用构建，没有重新构建或换版。

它只读取通过资格校验的原生RAM计数，不映射BAR0，不写MMIO；完整MMIO方案没有部署。
探针没有报告新事件不能被写成“没有欠载”或“扫描输出正常”。
本机运行状态与全部文件指纹见[当前EFI](../Current-EFI.md)。
