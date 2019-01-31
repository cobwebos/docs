---
title: Azure 备份支持矩阵
description: 汇总 Azure 备份服务的支持设置和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cb3a60995a4edfe5eb00f1a5e88812146816806a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883698"
---
# <a name="azure-backup-support-matrix"></a>Azure 备份支持矩阵

可以使用 [Azure 备份服务](backup-overview.md)将数据备份到 Microsoft Azure 云。 本文汇总了 Azure 备份方案和部署的支持设置和限制。

## <a name="vault-support"></a>保管库支持

Azure 备份使用恢复服务保管库来安排和管理备份以及存储备份的数据。

**设置** | **详细信息**
--- | ---
保管库的数目 | 单个订阅中最多可以有 500 个恢复服务保管库。
保管库中的计算机数 | 单个保管库中最多可以有 1000 个 Azure VM。<br/><br/> 最多可以在单个保管库中注册 50 台运行 Azure 备份代理（Microsoft Azure 恢复服务代理 (MABS)）的本地计算机。
保管库存储中的数据源 | 最多 54400 GB。 Azure VM 备份没有限制。
到保管库的备份 | Azure VM：一天一次；受 DPM/MABS 保护的计算机：一天两次；直接使用 MARS 代理备份的计算机：一天三次。  
移动保管库 | 若要移动恢复服务保管库，必须注册专用预览版。 若要试用，请发邮件至 AskAzureBackupTeam@microsoft.com。
在保管库之间移动数据 | 不支持在保管库之间移动备份的数据。
存储复制类型 | 可以在存储备份之前修改保管库的存储复制类型 (GRS/LRS)。 在保管库中开始备份以后，就不能修改复制类型。



## <a name="on-premises-backup-support"></a>本地备份支持

下面是在需要备份本地计算机的情况下的支持项目。

**计算机** | **位置** | **备份** | **功能**
--- | --- | --- | ---
**Windows 物理机/虚拟机（无备份服务器）** | 文件、文件夹、系统状态 | 备份到恢复服务保管库 | 一天备份三次。<br/><br/> 无应用感知型备份。<br/><br/> 还原文件、文件夹、卷。
**Linux 物理机/虚拟机（无备份服务器）** | 不支持备份。
**装有 DPM 的物理机/虚拟机** | 文件、文件夹、卷、系统状态、应用数据。 | 备份到 DPM（备份到以本地方式附加到 DPM 服务器的磁盘，或者备份到磁带）。<br/><br/> DPM 然后备份到保管库。 | 应用感知型快照<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/>. 不支持 Oracle。
**装有 MABS 的物理机/虚拟机** | 文件、文件夹、卷、系统状态、应用数据。 | 备份到 MABS（备份到以本地方式附加到 MABS 服务器的磁盘）。 不支持磁带<br/><br/> MABS 然后备份到保管库。 | 应用感知型快照<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/>. 不支持 Oracle。


## <a name="azure-vms"></a>Azure VM

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **详细信息**
--- | ---
Azure VM 数据磁盘 | 限制为 16 个。
Azure VM 数据磁盘大小 | 单个磁盘最大可以为 4095 GB。


### <a name="azure-vm-backup-options"></a>Azure VM 备份选项

下面是在需要备份 Azure VM 的情况下的支持项目。

**计算机** | **位置** | **备份** | **功能**
--- | --- | --- | ---
**Azure VM（无备份服务器）** | 文件、文件夹、系统状态 | 备份到保管库。 | 一天备份一次。<br/><br/> 应用感知型备份适用于 Windows VM，文件一致性备份适用于 Linux VM。 可以使用自定义脚本配置 Linux 计算机的应用一致性。<br/><br/> 还原 VM/磁盘。<br/><br/> 不能在 Azure VM 上备份到本地位置。
**装有 DPM 的 Azure VM** | 文件、文件夹、卷、系统状态、应用数据。 | 备份到在 Azure 中运行的 DPM（备份到以本地方式附加到 DPM 服务器的磁盘）。 不支持磁带。<br/><br/> DPM 然后备份到保管库。 | 应用感知型快照<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/>. 不支持 Oracle。
**装有 MABS 的 Azure VM** | 文件、文件夹、卷、系统状态、应用数据。 | 备份到在 Azure 中运行的 MABS（备份到以本地方式附加到 MABS 服务器的磁盘）。 不支持磁带<br/><br/> MABS 然后备份到保管库。 | 应用感知型快照<br/><br/> 备份和恢复的完整粒度。<br/><br/> 就 VM (Hyper-V/VMware) 来说，支持 Linux。<br/><br/>. 不支持 Oracle。





## <a name="linux-backup-support"></a>Linux 备份支持

下面是在需要备份 Linux 计算机的情况下的支持项目。

**备份** | **Linux（Azure 认可）**
--- | ---
**本地 Linux 计算机（没有 DPM 或 MABS）**。 | 不是。 MARS 代理只能安装在 Windows 计算机上。
**Azure VM（没有 DPM 或 MABS）** | 使用[自定义脚本](backup-azure-linux-app-consistent.md)的应用一致性备份。<br/><br/> 文件级恢复。<br/><br/> 通过从恢复点或磁盘创建 VM 进行还原。
**本地计算机/装有 DPM 的 Azure VM** | 在 Hyper-V 和 VMWare 上对 Linux 来宾 VM 进行文件一致性备份<br/><br/> 对 Hyper-V 和 VMWare Linux 来宾 VM 进行 VM 还原</br></br> 文件一致性备份不适用于 Azure VM
**本地计算机/装有 MABS 的 Azure VM** | 在 Hyper-V 和 VMWare 上对 Linux 来宾 VM 进行文件一致性备份<br/><br/> 对 Hyper-V 和 VMWare Linux 来宾 VM 进行 VM 还原</br></br> 文件一致性备份不适用于 Azure VM。

## <a name="disk-support"></a>磁盘支持

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

Azure 备份支持对备份流量进行压缩，详细情况汇总在下表中。 请注意：

- 就 Azure VM 来说，VM 扩展会通过存储网络直接读取 Azure 存储帐户中的数据，因此无需压缩此流量。
- 如果使用 DPM 或 MABS，则可先压缩数据，然后再将其备份到 DPM/MABS，以便节省带宽。

**计算机** | **压缩到 MABS/DPM (TCP)** | **压缩到保管库 (HTTPS)**
--- | --- | ---
没有 DPM/MABS 的本地 Windows 计算机 | NA | 是
Azure VM | NA | NA
本地计算机/装有 DPM 的 Azure VM | ![是][green] | ![是][green]
本地计算机/装有 MABS 的 Azure VM | ![是][green] | ![是][green]



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

- [备份 Azure VM](backup-azure-arm-vms-prepare.md)
- 不使用备份服务器[直接备份 Windows 计算机](tutorial-backup-windows-server-to-azure.md)。
- [设置 MABS](backup-azure-microsoft-azure-backup.md) 以备份到 Azure，然后将工作负荷备份到 MABS。
- [设置 DPM](backup-azure-dpm-introduction.md) 以备份到 Azure，然后将工作负荷备份到 DPM。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
