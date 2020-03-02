---
title: Azure 备份支持矩阵
description: 汇总 Azure 备份服务的支持设置和限制。
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: dd4ec646c786ee686567aa137845e583a4cf47e3
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206650"
---
# <a name="support-matrix-for-azure-backup"></a>Azure 备份的支持矩阵

可以使用[Azure 备份](backup-overview.md)将数据备份到 Microsoft Azure 云平台。 本文总结了 Azure 备份方案和部署的一般支持设置和限制。

提供其他支持矩阵：

- [Azure 虚拟机（VM）备份](backup-support-matrix-iaas.md)的支持矩阵
- 使用[System Center Data Protection Manager （DPM）/Microsoft Azure 备份服务器（MABS）](backup-support-matrix-mabs-dpm.md)进行备份的支持矩阵
- 使用[Microsoft Azure 恢复服务（MARS）代理](backup-support-matrix-mars-agent.md)进行备份的支持矩阵

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>保管库支持

Azure 备份使用恢复服务保管库来安排和管理备份。 它还使用保管库来存储备份的数据。

下表描述了恢复服务保管库的功能：

**功能** | **详细信息**
--- | ---
**订阅中的保管库数** | 单个订阅中最多可以有 500 个恢复服务保管库。
**保管库中的计算机数** | 单个保管库中最多1000个 Azure Vm。<br/><br/> 单个保管库中最多可注册 50 个 MABS 服务器。
**保管库存储中的数据源** | 最大 54400 GB。 Azure VM 备份没有限制。
**保管库备份** | **Azure vm：** 一天一次。<br/><br/>**受 DPM/MABS 保护的计算机：** 一天两次。<br/><br/> **使用 MARS 代理直接备份的计算机：** 一天三次。
**在保管库之间备份** | 备份在一个区域内进行。<br/><br/> 在包含想要备份的 VM 的每个 Azure 区域中都需要有一个保管库。 无法备份到其他区域。
**移动保管库** | 可以跨订阅或同一订阅中的资源组之间[移动保管库](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)。 但不支持跨区域移动保管库。
**在保管库之间移动数据** | 不支持在保管库之间移动备份数据。
**修改保管库存储类型** | 可以在存储备份之前修改保管库的存储复制类型（异地冗余存储或本地冗余存储）。 在保管库中开始备份以后，就不能修改复制类型。

## <a name="on-premises-backup-support"></a>本地备份支持

如果要备份本地计算机，则支持以下是：

**计算机** | **备份的内容** | **位置** | **功能**
--- | --- | --- | ---
**使用 MARS 代理直接备份 Windows 计算机** | 文件、文件夹、系统状态 | 备份到恢复服务保管库。 | 每天备份三次<br/><br/> 无应用感知备份<br/><br/> 还原文件、文件夹、卷
**使用 MARS 代理直接备份 Linux 计算机** | 不支持备份
**备份到 DPM** | 文件、文件夹、卷、系统状态、应用数据 | 备份到本地 DPM 存储。 DPM 然后备份到保管库。 | 应用感知型快照<br/><br/> 用于备份和恢复的完全粒度<br/><br/> Vm 支持的 Linux （Hyper-v/VMware）<br/><br/> 不支持 Oracle
**备份到 MABS** | 文件、文件夹、卷、系统状态、应用数据 | 备份到 MABS 本地存储。 MABS 然后备份到保管库。 | 应用感知型快照<br/><br/> 用于备份和恢复的完全粒度<br/><br/> Vm 支持的 Linux （Hyper-v/VMware）<br/><br/> 不支持 Oracle

## <a name="azure-vm-backup-support"></a>Azure VM 备份支持

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **详细信息**
--- | ---
**Azure VM 数据磁盘** | 限制为16 <br> 若要注册获取包含 16 个以上磁盘（最多 32 个磁盘）的个人预览版 VM，请向我们发送电子邮件：AskAzureBackupTeam@microsoft.com
**Azure VM 数据磁盘大小** | 对于 VM 中的所有磁盘，单独的磁盘大小可以高达 32 TB，最大为 256 TB。

### <a name="azure-vm-backup-options"></a>Azure VM 备份选项

如果要备份 Azure Vm，则支持以下是：

**计算机** | **备份的内容** | **位置** | **功能**
--- | --- | --- | ---
**使用 VM 扩展的 Azure VM 备份** | 整个 VM | 备份到保管库。 | 为 VM 启用备份时安装的扩展。<br/><br/> 每天备份一次。<br/><br/> 适用于 Windows Vm 的应用程序感知备份;适用于 Linux Vm 的文件一致性备份。 你可以使用自定义脚本为 Linux 计算机配置应用程序一致性。<br/><br/> 还原 VM 或磁盘。<br/><br/> 无法将 Azure VM 备份到本地位置。
**使用 MARS 代理备份 Azure VM** | 文件、文件夹、系统状态 | 备份到保管库。 | 每天备份三次。<br/><br/> 如果要备份特定文件或文件夹，而不是整个 VM，则 MARS 代理可以与 VM 扩展一起运行。
**装有 DPM 的 Azure VM** | 文件、文件夹、卷、系统状态、应用数据 | 备份到运行 DPM 的 Azure VM 的本地存储。 DPM 然后备份到保管库。 | 应用感知快照。<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/> 不支持 Oracle。
**装有 MABS 的 Azure VM** | 文件、文件夹、卷、系统状态、应用数据 | 备份到运行 MABS 的 Azure VM 的本地存储。 MABS 然后备份到保管库。 | 应用感知快照。<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/> 不支持 Oracle。

## <a name="linux-backup-support"></a>Linux 备份支持

如果要备份 Linux 计算机，则支持以下是：

**备份类型** | **Linux（Azure 认可）**
--- | ---
**直接备份运行 Linux 的本地计算机** | 不受支持。 MARS 代理只能安装在 Windows 计算机上。
**使用代理扩展来备份运行 Linux 的 Azure VM** | 使用[自定义脚本](backup-azure-linux-app-consistent.md)的应用程序一致性备份。<br/><br/> 文件级恢复。<br/><br/> 通过从恢复点或磁盘创建 VM 进行还原。
**使用 DPM 备份运行 Linux 的本地计算机** | Hyper-v 和 VMWare 上对 Linux 来宾 Vm 进行文件一致性备份。<br/><br/> Hyper-v 和 VMWare Linux 来宾 Vm 的 VM 还原。
**使用 MABS 备份运行 Linux 的本地计算机** | Hyper-v 和 VMWare 上对 Linux 来宾 Vm 进行文件一致性备份。<br/><br/> Hyper-v 和 VMWare Linux 来宾 Vm 的 VM 还原。
**使用 MABS 或 DPM 备份 Linux Azure Vm** | 不受支持。

## <a name="daylight-saving-time-support"></a>夏令时支持

对于 Azure VM 备份，azure 备份不支持夏令时自动时钟调整。 请根据需要手动修改备份策略。

## <a name="disk-deduplication-support"></a>磁盘重复数据删除支持

磁盘重复数据删除支持如下：

- 使用 DPM 或 MABs 备份运行 Windows 的 Hyper-v Vm 时，本地支持磁盘重复数据删除。 Windows Server 在以备份存储形式附加到 VM 的虚拟硬盘（Vhd）上执行重复数据删除（在主机级别）。
- 在 Azure 中，任何备份组件都不支持重复数据删除。 当 DPM 和 MABS 部署在 Azure 中时，附加到 VM 的存储磁盘无法进行重复数据删除。

## <a name="security-and-encryption-support"></a>安全和加密支持

Azure 备份支持加密传输和静态数据。

### <a name="network-traffic-to-azure"></a>到 Azure 的网络流量

- 从服务器到恢复服务保管库的备份流量通过高级加密标准 256 进行加密。
- 备份数据通过安全 HTTPS 链接进行发送。
- 备份数据以加密格式存储在恢复服务保管库中。
- 只有你有解锁此数据的通行短语。 Microsoft 无法解密任何恢复点的备份数据。

    > [!WARNING]
    > 设置保管库后，只有你才能访问加密密钥。 Microsoft 不保留副本，且没有访问该密钥的权限。 如果客户丢失了密钥，Microsoft 无法恢复备份数据。

### <a name="data-security"></a>数据安全性

- 在备份 Azure Vm 时，需要在虚拟机*中*设置加密。
- Azure 备份支持 Azure 磁盘加密，后者在 Windows 虚拟机上使用 BitLocker，在 Linux 虚拟机上使用 **dm-crypt**。
- 在后端，Azure 备份使用[azure 存储服务加密](../storage/common/storage-service-encryption.md)来保护静态数据。

**计算机** | **传输中** | **静态**
--- | --- | ---
**无 DPM/MABS 的本地 Windows 计算机** | ![是][green] | ![是][green]
**Azure VM** | ![是][green] | ![是][green]
**本地 Windows 计算机或包含 DPM 的 Azure Vm** | ![是][green] | ![是][green]
**本地 Windows 计算机或包含 MABS 的 Azure Vm** | ![是][green] | ![是][green]

## <a name="compression-support"></a>压缩支持

Azure 备份支持对备份流量进行压缩，详细情况汇总在下表中。

- 对于 Azure Vm，VM 扩展会通过存储网络直接读取 Azure 存储帐户中的数据，因此无需压缩此流量。
- 如果你使用的是 DPM 或 MABS，则可以通过在备份数据前对其进行压缩来节省带宽。

**计算机** | **压缩到 MABS/DPM (TCP)** | **压缩到保管库（HTTPS）**
--- | --- | ---
**直接备份本地 Windows 计算机** | NA | ![是][green]
**使用 VM 扩展对 Azure Vm 进行备份** | NA | NA
**使用 MABS/DPM 在本地/Azure 计算机上备份** | ![是][green] | ![是][green]

## <a name="retention-limits"></a>保留期限制

**设置** | **限制**
--- | ---
**每个受保护实例的最大恢复点数（计算机或工作负荷）** | 9999
**恢复点的最大到期时间** | 无限制
**DPM/MABS 的最大备份频率** | SQL Server 每隔 15 分钟<br/><br/> 对于其他工作负荷每小时一次
**保管库的最大备份频率** | **运行 MARS 的本地 Windows 计算机或 Azure vm：** 每日三个<br/><br/> **DPM/MABS：** 每日2次<br/><br/> **AZURE VM 备份：** 每天一次
**恢复点保留期** | 每日、每周、每月、每年
**最长保持期** | 取决于备份频率
**DPM/MABS 磁盘上的恢复点** | 对于文件服务器为 64;适用于应用服务器的448 <br/><br/>本地 DPM 无限制的磁带恢复点

## <a name="cross-region-restore"></a>跨区域还原

Azure 备份添加了跨区域还原功能来增强数据可用性和复原能力，使客户能够完全控制将数据还原到次要区域。 若要配置此功能，请访问[Set 跨区域还原一文。](backup-create-rs-vault.md#set-cross-region-restore) 以下管理类型支持此功能：

| 备份管理类型 | 支持                                                    | 支持的区域 |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | 可以。 对于超过 4 TB 磁盘的加密 Vm 和 Vm 支持公共有限预览版 | 美国中西部   |
| MARS 代理/本地 | 是                                                           | 不可用               |
| SQL/SAP HANA          | 是                                                           | 不可用               |
| AFS                    | 是                                                           | 不可用               |



## <a name="next-steps"></a>后续步骤

- [查看 Azure VM 备份的支持矩阵](backup-support-matrix-iaas.md)。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
