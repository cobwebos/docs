* 如果非托管磁盘位于此前使用 Azure Storage Service Encryption 加密的存储帐户中，则不能将其转换成托管磁盘。 如需在托管磁盘中复制和使用这些虚拟硬盘 (VHD) 的步骤，请参阅本文后面的[托管磁盘和 Azure Storage Service Encryption](#managed-disks-and-azure-storage-service-encryption) 部分。

* 该转换需要重启 VM，因此请在预先存在的维护时段内计划 VM 迁移。 

* 转换是不可逆的。 

* 请务必测试转换。 在生产环境中执行迁移之前迁移测试性虚拟机。

* 在转换期间，将解除分配 VM。 转换完成后，VM 在启动时会接收新的 IP 地址。 如果需要，可向 VM [分配静态 IP 地址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)。

* 不会删除在转换之前由 VM 使用的原始 VHD 和存储帐户。 它们会继续产生费用。 若要避免这些项目产生的费用，请在验证转换已完成后删除原始的 VHD Blob。