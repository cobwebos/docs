---
title: Azure 备份支持矩阵
description: 汇总 Azure 备份服务的支持设置和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 99dd3c0b07307f2d0bf97dbff697e32e648705ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66400180"
---
# <a name="azure-backup-support-matrix"></a>Azure 备份支持矩阵

可以使用[Azure 备份](backup-overview.md)将数据备份到 Microsoft Azure 云平台。 本文汇总了常规支持设置和 Azure 备份方案和部署的限制。

提供其他支持矩阵：

- 支持矩阵[Azure 虚拟机 (VM) 备份](backup-support-matrix-iaas.md)
- 通过使用备份的支持矩阵[System Center Data Protection Manager (DPM) / Microsoft Azure 备份服务器 (MABS)](backup-support-matrix-mabs-dpm.md)
- 通过使用备份的支持矩阵[Microsoft Azure 恢复服务 (MARS) 代理](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>保管库支持

Azure 备份使用恢复服务保管库来安排和管理备份。 它还使用保管库来存储备份数据。

下表描述了恢复服务保管库的功能：

**功能** | **详细信息**
--- | ---
**订阅中的保管库数** | 单个订阅中最多可以有 500 个恢复服务保管库。
**保管库中的计算机数** | 在单个保管库中的最多 1,000 台 Azure Vm。<br/><br/> 单个保管库中最多可注册 50 个 MABS 服务器。
**保管库存储中的数据源** | 最大 54,400 GB。 Azure VM 备份没有限制。
**保管库备份** | **Azure Vm:** 每天一次。<br/><br/>**使用 DPM/MABS 保护的计算机：** 每天两次。<br/><br/> **备份直接通过使用 MARS 代理的计算机：** 一天三次。
**在保管库之间备份** | 备份在一个区域内进行。<br/><br/> 在包含想要备份的 VM 的每个 Azure 区域中都需要有一个保管库。 无法备份到其他区域。
**移动保管库** | 你可以[移动保管库](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)跨订阅或同一订阅中的资源组之间。
**在保管库之间移动数据** | 保管库之间移动备份数据不受支持。
**修改保管库存储类型** | 之前的备份存储，可以修改一个保管库的存储复制类型 （异地冗余存储或本地冗余存储）。 在保管库中开始备份以后，就不能修改复制类型。

## <a name="on-premises-backup-support"></a>本地备份支持

下面是如果你想要备份的本地计算机支持的功能：

**计算机** | **备份的内容** | **位置** | **功能**
--- | --- | --- | ---
**使用 MARS 代理直接备份 Windows 计算机** | 文件、文件夹、系统状态 | 备份到恢复服务保管库。 | 将一天三次备份<br/><br/> 没有应用程序感知备份<br/><br/> 还原文件、 文件夹、 卷
**使用 MARS 代理直接备份 Linux 计算机** | 不支持备份
**备份到 DPM** | 文件、 文件夹、 卷、 系统状态、 应用程序数据 | 备份到本地 DPM 存储。 DPM 然后备份到保管库。 | 应用感知型快照<br/><br/> 完整备份和恢复粒度<br/><br/> Linux Vm (Hyper-v/VMware) 支持<br/><br/> Oracle 不支持
**备份到 MABS** | 文件、 文件夹、 卷、 系统状态、 应用程序数据 | 备份到 MABS 本地存储。 MABS 然后备份到保管库。 | 应用感知型快照<br/><br/> 完整备份和恢复粒度<br/><br/> Linux Vm (Hyper-v/VMware) 支持<br/><br/> Oracle 不支持

## <a name="azure-vm-backup-support"></a>Azure VM 备份支持

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **详细信息**
--- | ---
**Azure VM 数据磁盘** | 限制为 16
**Azure VM 数据磁盘大小** | 单个磁盘可以最大 4,095 GB

### <a name="azure-vm-backup-options"></a>Azure VM 备份选项

下面是如果你想要备份 Azure Vm 支持的功能：

**计算机** | **备份的内容** | **位置** | **功能**
--- | --- | --- | ---
**使用 VM 扩展的 azure VM 备份** | 整个 VM | 备份到保管库。 | 为 VM 启用备份时安装的扩展。<br/><br/> 备份一天一次。<br/><br/> 适用于 Windows Vm; 应用程序感知备份适用于 Linux Vm 的文件一致性备份。 可以通过使用自定义脚本来配置应用程序一致性对于 Linux 计算机。<br/><br/> 还原 VM 或磁盘。<br/><br/> 不能在本地位置到 Azure VM 备份。
**使用 MARS 代理的 azure VM 备份** | 文件、文件夹、系统状态 | 备份到保管库。 | 备份一天三次。<br/><br/> 如果你想要备份的特定文件或文件夹，而不是整个 VM，MARS 代理可以运行 VM 扩展的旁边。
**装有 DPM 的 Azure VM** | 文件、 文件夹、 卷、 系统状态、 应用程序数据 | 备份到运行 DPM 的 Azure VM 的本地存储。 DPM 然后备份到保管库。 | 应用感知快照。<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/> 不支持 Oracle。
**装有 MABS 的 Azure VM** | 文件、 文件夹、 卷、 系统状态、 应用程序数据 | 备份到 Azure vm 的运行 MABS 的本地存储。 MABS 然后备份到保管库。 | 应用感知快照。<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/> 不支持 Oracle。

## <a name="linux-backup-support"></a>Linux 备份支持

下面是如果你想要备份 Linux 机支持的功能：

**备份类型** | **Linux（Azure 认可）**
--- | ---
**本地计算机上运行 Linux 的直接备份** | 不支持。 可以仅在 Windows 计算机上安装 MARS 代理。
**用于备份运行 Linux 的 Azure VM 代理扩展** | 通过使用应用程序一致性备份[自定义脚本](backup-azure-linux-app-consistent.md)。<br/><br/> 文件级恢复。<br/><br/> 通过从恢复点或磁盘创建 VM 进行还原。
**使用 DPM 备份的本地或运行 Linux 的 Azure VM** | Linux 来宾 vm 的 HYPER-V 和 VMWare 上的文件一致性备份。<br/><br/> HYPER-V 和 VMWare Linux 来宾 Vm 的 VM 还原。<br/><br/> 文件一致性备份不适用于 Azure VM。
**使用 MABS 备份的本地计算机或运行 Linux 的 Azure VM** | Linux 来宾 vm 的 HYPER-V 和 VMWare 上的文件一致性备份。<br/><br/> HYPER-V 和 VMWare Linux 来宾 Vm 的 VM 还原。<br/><br/> 文件一致性备份不适用于 Azure VM。

## <a name="daylight-saving-time-support"></a>夏时制的支持

Azure 备份不支持自动时钟调整为夏时制为 Azure VM 备份。 请根据需要手动修改备份策略。

## <a name="disk-deduplication-support"></a>磁盘重复数据删除支持

磁盘重复数据删除支持如下：

- 使用 DPM 或 MABs 备份正在运行 Windows 的 HYPER-V Vm 时，磁盘重复数据删除是支持的本地。 Windows Server 虚拟硬盘 (Vhd) 附加到 VM 作为备份存储上执行 （在主机级别） 的重复数据删除。
- 在 Azure 中，任何备份组件都不支持重复数据删除。 在 Azure 中部署 DPM 和 MABS，存储磁盘附加到 VM 不能进行重复数据删除。

## <a name="security-and-encryption-support"></a>安全和加密支持

Azure 备份支持的传输中和静态数据加密。

### <a name="network-traffic-to-azure"></a>到 Azure 的网络流量

- 从服务器到恢复服务保管库备份流量使用高级加密标准 256 进行加密。
- 备份数据通过安全 HTTPS 链接进行发送。
- 备份数据存储以加密形式在恢复服务保管库。
- 只有你有解锁此数据的通行短语。 Microsoft 无法解密任何恢复点的备份数据。

    > [!WARNING]
    > 设置保管库后，只有你才能访问加密密钥。 Microsoft 不保留副本，且没有访问该密钥的权限。 如果客户丢失了密钥，Microsoft 无法恢复备份数据。

### <a name="data-security"></a>数据安全

- 当您要备份 Azure Vm 时，需要设置加密*内*虚拟机。
- Azure 备份支持 Azure 磁盘加密，后者在 Windows 虚拟机上使用 BitLocker，在 Linux 虚拟机上使用 **dm-crypt**。
- 在后端，使用 Azure 备份[Azure 存储服务加密](../storage/common/storage-service-encryption.md)，这有助于保护静态数据。

**计算机** | **传输中** | **静态**
--- | --- | ---
**而无需 DPM/MABS 的本地 Windows 计算机** | ![是][green] | ![是][green]
**Azure VM** | ![是][green] | ![是][green]
**在本地 Windows 计算机或使用 DPM 的 Azure Vm** | ![是][green] | ![是][green]
**在本地 Windows 计算机或带有 MABS 的 Azure Vm** | ![是][green] | ![是][green]

## <a name="compression-support"></a>压缩支持

下表中进行了总结，backup 支持备份流量的压缩。

- 对于 Azure Vm，VM 扩展读取的数据直接从 Azure 存储帐户通过存储网络，因此无需优化此流量。
- 如果你使用 DPM 或 MABS，可以通过将数据之前备份压缩来节省带宽。

**计算机** | **压缩到 MABS/DPM (TCP)** | **压缩到保管库 (HTTPS)**
--- | --- | ---
**直接备份本地 Windows 计算机** | NA | ![是][green]
**使用 VM 扩展的 Azure Vm 备份** | NA | NA
**本地/Azure 使用 MABS/DPM 的计算机上的备份** | ![是][green] | ![是][green]

## <a name="retention-limits"></a>保留期限制

**设置** | **限制**
--- | ---
**每个受保护实例 （计算机或工作负荷） 的最大恢复点** | 9,999
**最大恢复点到期时间** | 无限制
**到 DPM/MABS 最大备份频率** | SQL Server 每隔 15 分钟<br/><br/> 对于其他工作负荷小时一次
**最大备份频率到保管库** | **在本地 Windows 计算机或运行 MARS 的 Azure Vm:** 三个每日<br/><br/> **DPM/MABS:** 每天两次<br/><br/> **Azure VM 备份：** 每天一个
**恢复点保留期** | 每日、每周、每月、每年
**最大保持期** | 取决于备份频率
**DPM/MABS 磁盘上的恢复点** | 对于文件服务器; 如果为 64应用程序服务器为 448 <br/><br/>在本地 DPM 的无限制的磁带恢复点

## <a name="next-steps"></a>后续步骤

- [查看 Azure VM 备份的支持矩阵](backup-support-matrix-iaas.md)。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
