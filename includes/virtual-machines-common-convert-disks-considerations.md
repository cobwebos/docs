
* 该转换需要重启 VM，因此请在预先存在的维护时段内计划 VM 迁移。 

* 转换是不可逆的。 

* 请务必测试转换。 在生产环境中执行迁移之前迁移测试性虚拟机。

* 在转换期间，将解除分配 VM。 转换完成后，VM 在启动时会接收新的 IP 地址。 如果需要，可向 VM [分配静态 IP 地址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)。

* 不会删除在转换之前由 VM 使用的原始 VHD 和存储帐户。 它们会继续产生费用。 若要避免这些项目产生的费用，请在验证转换已完成后删除原始的 VHD Blob。

* 查看 Azure VM 代理支持转换过程所需的最低版本。 有关如何检查和更新代理版本的信息，请参阅 [Azure 中 VM 代理的最低版本支持](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)