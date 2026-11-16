# 未部署的 S0ix 睡眠候选

从本仓库集成的G83上游1.4.6配置保留：S0ixStandby0.5.4、SSDT-DEEPIDLE与两个
`Enabled=false`条目。上游来源为 `ed57bf051d97578ce8c1afb8e6f2338335be2de1`；
S0ix来源为labrouss/S0ixStandby `c52f172b934c0f906fccb74ad2bce0a974ca5bb1`。
原BSD-3-Clause许可随kext保留。

根EFI已对齐本机实际组合，因此这两个未部署组件移到此目录；`profile.json`保存配置片段。
它们没有被删除出维护线，也没有自动启用。上游睡眠结果来自另一台机器与Sequoia，
不能作为本机Tahoe的验收。HDMI唤醒及接电dark-wake限制见
[集成记录](../../docs/Integration-20261116.md)。

后续需成组评审Ice Lake CPUID、ACPI、kext与电源策略，准备可实际使用的回退入口，
取得新实验授权后再单独部署；不能仅将两个Enabled改为true或自动改pmset。
