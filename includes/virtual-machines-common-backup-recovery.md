
## <a name="azure-backup"></a>Azure 备份

若要备份运行生产工作负荷的 Azure VM，请使用 Azure 备份。 Azure 备份对 Windows 和 Linux VM 均支持应用程序一致性备份。 Azure 备份可创建恢复点，这些恢复点存储在异地冗余的恢复保管库中。 从恢复点还原时，可以还原整个 VM，也可以仅还原特定的文件。 

有关适用于 Azure VM 的 Azure 备份动手实践简介，请参阅适用于 [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) 或 [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md) 的“备份 Azure 虚拟机”教程。

有关 Azure 备份工作原理的详细信息，请参阅[在 Azure 中计划 VM 备份基础结构](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

当整个区域因重大自然灾难或大规模服务中断而发生中断时，Azure Site Recovery 可以保护 VM，使其免受重大灾难影响。 可为 VM 配置 Azure Site Recovery，从而只需单击一下便可在几分钟内恢复应用程序。 可复制到所选的 Azure 区域（并不局限于配对区域）。 

可以通过按需测试故障转移进行灾难恢复演练，而不影响生产工作负荷或正在进行的复制。 创建恢复计划，为多个 VM 上运行的整个应用程序安排故障转移和故障回复。 恢复计划功能将与 Azure 自动化 Runbook 集成。

可通过[复制虚拟机](https://aka.ms/a2a-getting-started)入门。 

## <a name="managed-snapshots"></a>托管快照 

在开发和测试环境中，快照为使用托管磁盘的 VM 备份提供快速而又简单的选项。 托管快照是托管磁盘的只读完整副本。 快照独立于源磁盘而存在，并可用来新建用于重建 VM 的托管磁盘。 基于磁盘的已使用部分对快照进行计费。 例如，如果创建预配容量为 64 GB、实际使用数据大小为 10 GB 的托管磁盘的快照，将仅针对已用数据大小 10 GB 对该快照进行计费。  

有关创建快照的详细信息，请参阅：

* [在 Windows 中使用快照创建存储为托管磁盘的 VHD 的副本](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [在 Linux 中使用快照创建存储为托管磁盘的 VHD 的副本](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>后续步骤
可以按照适用于 [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) 或 [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md) 的“备份 Windows 虚拟机教程”试用 Azure 备份。
