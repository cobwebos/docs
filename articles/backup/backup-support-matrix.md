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
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429564"
---
# <a name="azure-backup-support-matrix"></a>Azure 备份支持矩阵

可以使用 [Azure 备份服务](backup-overview.md)将数据备份到 Microsoft Azure 云。 本文汇总了 Azure 备份方案和部署的一般支持设置和限制。

提供其他支持矩阵：

适用于 Azure VM 备份的[支持矩阵](backup-support-matrix-iaas.md) 适用于使用 System Center DPM/Microsoft Azure 备份服务器 (MABS) 的备份的[支持矩阵](backup-support-matrix-mabs-dpm.md) 适用于使用 Microsoft Azure 恢复服务 (MARS) 代理的备份的[支持矩阵](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>保管库支持

Azure 备份使用恢复服务保管库来安排和管理备份以及存储备份的数据。

**设置** | **详细信息**
--- | ---
**订阅中的保管库数** | 单个订阅中最多可以有 500 个恢复服务保管库。
**保管库中的计算机数** | 单个保管库中最多可以有 1000 个 Azure VM。<br/><br/> 单个保管库中最多可注册 50 个 MABS 服务器。
**保管库存储中的数据源** | 最多 54400 GB。 Azure VM 备份没有限制。
**保管库备份** | Azure VM：每天备份一次<br/><br/>受 DPM/MABS 保护的计算机：一天两次<br/><br/> 直接使用 MARS 代理备份的计算机：一天三次。 
**在保管库之间备份** | 备份在一个区域内进行。<br/><br/> 在包含想要备份的 VM 的每个 Azure 区域中都需要有一个保管库。 无法备份到其他区域。 
**移动保管库** | 可以跨订阅或同一订阅中的资源组之间[移动保管库](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)。
**在保管库之间移动数据** | 不支持在保管库之间移动备份的数据。
**修改保管库存储类型** | 可以在存储备份之前修改保管库的存储复制类型 (GRS/LRS)。 在保管库中开始备份以后，就不能修改复制类型。



## <a name="on-premises-backup-support"></a>本地备份支持

下面是在需要备份本地计算机的情况下的支持项目。

**计算机** | **已备份对象** | **位置** | **功能**
--- | --- | --- | ---
**使用 MARS 代理直接备份 Windows 计算机** | 文件、文件夹、系统状态 | 备份到恢复服务保管库 | 一天备份三次。<br/><br/> 无应用感知型备份。<br/><br/> 还原文件、文件夹、卷。
**使用 MARS 代理直接备份 Linux 计算机** | 不支持备份。
**备份到 DPM** | 文件、文件夹、卷、系统状态、应用数据。 | 备份到本地 DPM 存储。 DPM 然后备份到保管库。 | 应用感知型快照<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/>. 不支持 Oracle。
**备份到 MABS** | 文件、文件夹、卷、系统状态、应用数据。 | 备份到 MABS 本地存储。 MABS 然后备份到保管库。 | 应用感知型快照<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/>. 不支持 Oracle。


## <a name="azure-vm-backup-support"></a>Azure VM 备份支持

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **详细信息**
--- | ---
Azure VM 数据磁盘 | 限制为 16 个。
Azure VM 数据磁盘大小 | 单个磁盘最大可以为 4095 GB。


### <a name="azure-vm-backup-options"></a>Azure VM 备份选项

下面是在需要备份 Azure VM 的情况下的支持项目。

**计算机** | **已备份对象** | **位置** | **功能**
--- | --- | --- | ---
**使用 VM 扩展的 Azure VM 备份** | 整个 VM | 备份到保管库 | 为 VM 启用备份时安装的扩展。<br/><br/> 一天备份一次。<br/><br/> 应用感知型备份适用于 Windows VM，文件一致性备份适用于 Linux VM。 可以使用自定义脚本配置 Linux 计算机的应用一致性。<br/><br/> 还原 VM/磁盘。<br/><br/> 不能在 Azure VM 上备份到本地位置。
**使用 MARS 代理的 Azure VM 备份** | 文件、文件夹 | 备份到保管库 | 一天备份三次。<br/><br/> 如果想要备份特定文件/文件夹，而不是备份整个 VM，MARS 代理可以随 VM 扩展运行。
**装有 DPM 的 Azure VM** | 文件、文件夹、卷、系统状态、应用数据。 | 备份到运行 DPM 的 Azure VM 的本地存储。 DPM 然后备份到保管库。 | 应用感知型快照<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/>. 不支持 Oracle。
**装有 MABS 的 Azure VM** | 文件、文件夹、卷、系统状态、应用数据。 | 备份到运行 MABS 的 Azure VM 的本地存储。 MABS 然后备份到保管库。 | 应用感知型快照<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/> 不支持 Oracle。





## <a name="linux-backup-support"></a>Linux 备份支持

下面是在需要备份 Linux 计算机的情况下的支持项目。

**备份** | **Linux（Azure 认可）**
--- | ---
**直接备份运行 Linux 的本地计算机**。 | 不是。 MARS 代理只能安装在 Windows 计算机上。
**备份运行 Linux 的 Azure VM（使用代理扩展）** | 使用[自定义脚本](backup-azure-linux-app-consistent.md)的应用一致性备份。<br/><br/> 文件级恢复。<br/><br/> 通过从恢复点或磁盘创建 VM 进行还原。
**备份本地计算机或运行 Linux 的 Azure VM（使用 DPM）** | 在 Hyper-V 和 VMWare 上对 Linux 来宾 VM 进行文件一致性备份<br/><br/> 对 Hyper-V 和 VMWare Linux 来宾 VM 进行 VM 还原</br></br> 文件一致性备份不适用于 Azure VM
**备份本地计算机/运行 Linux 的 Azure VM（使用 MABS）** | 在 Hyper-V 和 VMWare 上对 Linux 来宾 VM 进行文件一致性备份<br/><br/> 对 Hyper-V 和 VMWare Linux 来宾 VM 进行 VM 还原</br></br> 文件一致性备份不适用于 Azure VM。

## <a name="daylight-saving-support"></a>夏令时支持

Azure 备份不支持根据 Azure VM 备份的夏令时时差自动调整时钟。 请根据需要手动修改备份策略。


## <a name="disk-deduplication-support"></a>磁盘重复数据删除支持

磁盘重复数据删除支持如下：
- 使用 DPM 或 MABS 备份运行 Windows 的 Hyper-V VM 时，支持在本地进行磁盘重复数据删除。 Windows Server 会在以备份存储形式附加到虚拟机的虚拟硬盘 (VHD) 上执行主机级别的重复数据删除。
- 在 Azure 中，任何备份组件都不支持重复数据删除。 如果 System Center DPM 和备份服务器部署在 Azure 中，则附加到 VM 的存储磁盘无法进行重复数据删除。


## <a name="securityencryption-support"></a>安全性/加密支持

Azure 备份支持针对传输中数据和静态数据的加密：

发往 Azure 的网络流量：
- 从服务器到恢复服务保管库的备份流量通过高级加密标准 256 进行加密。
- 备份数据通过安全 HTTPS 链接进行发送。
- 备份数据以加密格式存储在恢复服务保管库中。
- 只有你有解锁此数据的通行短语。 Microsoft 无法解密任何恢复点的备份数据。
    > [!WARNING]
    > 设置保管库后，只有你才能访问加密密钥。 Microsoft 不保留副本，且没有访问该密钥的权限。 如果客户丢失了密钥，Microsoft 无法恢复备份数据。
数据安全：
- 备份 Azure VM 时，需要在虚拟机内部设置加密。
- Azure 备份支持 Azure 磁盘加密，后者在 Windows 虚拟机上使用 BitLocker，在 Linux 虚拟机上使用 **dm-crypt**。
- 在后端，Azure 备份使用 [Azure 存储服务加密](../storage/common/storage-service-encryption.md)来保护静态数据。


**计算机** | **传输中** | **静态**
--- | --- | ---
没有 DPM/MABS 的本地 Windows 计算机 | ![是][green] | ![是][green]
Azure VM | ![是][green] | ![是][green]
本地计算机/装有 DPM 的 Azure VM | ![是][green] | ![是][green]
本地计算机/装有 MABS 的 Azure VM | ![是][green] | ![是][green]



## <a name="compression-support"></a>压缩支持

Azure 备份支持对备份流量进行压缩，详细情况汇总在下表中。 

- 就 Azure VM 来说，VM 扩展会通过存储网络直接读取 Azure 存储帐户中的数据，因此无需压缩此流量。
- 如果使用 DPM 或 MABS，则可先压缩数据，然后再将其备份到 DPM/MABS，以便节省带宽。

**计算机** | **压缩到 MABS/DPM (TCP)** | **压缩到保管库 (HTTPS)**
--- | --- | ---
**直接备份本地 Windows 计算机** | NA | 是
**备份 Azure VM（使用 VM 扩展）** | NA | NA
**使用 MABS/DPM 备份本地计算机/Azure 计算机 | ![是][green] | ![是][green]



## <a name="retention-limits"></a>保留期限制

**设置** | **限制**
--- | ---
每个受保护实例（计算机/工作负荷）的恢复点数上限 | 9999
恢复点的最长到期时间 | 无限制
备份到 DPM/MABS 时的最高备份频率 | SQL Server 每隔 15 分钟<br/><br/> 其他工作负荷每小时一次。
备份到保管库时的最高备份频率 | 本地 Windows 计算机/运行 MARS 的 Azure VM：每天三次<br/><br/> DPM/MABS：每天两次<br/><br/> Azure VM 备份：每天 1 次
恢复点保留期 | 每日、每周、每月、每年。
最长数据保留期 | 取决于备份频率。
DPM/MABS 磁盘上的恢复点数 | 文件服务器为 64 个，应用服务器为 448 个。<br/><br/> 对于本地 DPM 来说，磁带恢复点没有限制。

## <a name="next-steps"></a>后续步骤

- [查看 Azure VM 备份的支持矩阵](backup-support-matrix-iaas.md)。


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
