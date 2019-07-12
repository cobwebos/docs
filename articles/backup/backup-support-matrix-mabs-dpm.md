---
title: Microsoft Azure 备份服务器和 System Center DPM 的支持矩阵
description: 本文汇总了使用 Microsoft Azure 备份服务器或 System Center DPM 备份本地和 Azure VM 资源时的 Azure 备份支持。
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 85e65c36a0d636d94a9ef9070c21ab047542d8f4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594258"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>使用 Microsoft Azure 备份服务器或 System Center DPM 备份的支持矩阵

可以使用[Azure 备份服务](backup-overview.md)备份的本地计算机和工作负荷和 Azure 虚拟机 (Vm)。 本文汇总了支持设置和使用 Microsoft Azure 备份服务器 (MABS) 或 System Center Data Protection Manager (DPM) 和 Azure 备份备份计算机的限制。

## <a name="about-dpmmabs"></a>有关 DPM/MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807)是一个企业解决方案的配置、 促进，以及管理备份和企业计算机和数据的恢复。 它是 [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) 产品套件的一部分。

MABS 是一种服务器产品，可用于备份的本地物理服务器、 Vm 和其上运行的应用。

MABS 取决于 System Center DPM 并提供类似的功能与两个区别：
- 无需 System Center 许可证即可运行 MABS。
- MABS 和 DPM，Azure 提供了备份的长期存储。 此外，DPM 允许在磁带上备份数据，以作长期存储。 MABS 不提供此功能。

下载从 MABS [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520)。 它可以运行在本地或 Azure VM 上。

DPM 和 MABS 支持备份各种应用、服务器和客户端操作系统。 它们提供多种备份方案：

- 可以使用系统状态或裸机备份在计算机级别进行备份。
- 可以备份特定的卷、共享、文件夹和文件。
- 可以通过使用优化的应用程序感知设置备份特定的应用。

## <a name="dpmmabs-backup"></a>DPM/MABS 备份

使用 DPM/MABS 和 Azure 备份进行备份的工作方式如下：

1. 将备份每台计算机上安装 DPM/MABS 保护代理。
1. 计算机和应用程序备份到本地存储在 DPM/MABS 上。
1. 在 DPM 服务器/MABS 上安装 Microsoft Azure 恢复服务 (MARS) 代理。
1. MARS 代理备份 DPM/MABS 磁盘到 Azure 的备份恢复服务保管库使用 Azure 备份。

更多相关信息：

- [详细了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 体系结构。
- [查看支持的功能](backup-support-matrix-mars-agent.md)MARS 代理。

## <a name="supported-scenarios"></a>支持的方案

**方案** | **代理** | **Location**
--- | --- | ---
**备份本地计算机/工作负荷** | 你想要备份的计算机上运行 DPM/MABS 保护代理。<br/><br/> DPM/MABS 服务器上在 MARS 代理。<br/> 启用此功能所需的 Microsoft Azure 恢复服务代理或 Azure 备份代理的最低版本为 2.0.8719.0。  | 必须在本地运行 DPM/MABS。
**Azure VM/工作负荷的备份** | 受保护计算机上的 DPM/MABS 保护代理。<br/><br/> DPM/MABS 服务器上在 MARS 代理。 | DPM/MABS 必须在 Azure VM 上运行。

## <a name="supported-deployments"></a>支持的部署

可以部署 DPM/MABS 下, 表中进行了总结。

**部署** | **支持** | **详细信息**
--- | --- | ---
**本地部署** | 物理服务器<br/><br/>Hyper-V VM<br/><br/> VMware VM | 如果 DPM/MABS 安装为 VMware VM，它仅备份 VMware 虚拟机和在这些 Vm 上运行的工作负荷。
**部署为 Azure Stack VM** | 仅限 MABS | 不能使用 DPM 来备份 Azure Stack VM。
**部署为 Azure VM** | 保护 Azure Vm 以及这些 Vm 运行的工作负荷 | 在 Azure 中运行 DPM/MABS 无法备份的本地计算机。


## <a name="supported-mabs-and-dpm-operating-systems"></a>支持的 MABS 和 DPM 操作系统

Azure 备份可以备份 DPM/MABS 实例正在运行任何以下操作系统。 操作系统应该运行最新的服务包和更新。

**方案** | **DPM/MABS**
--- | ---
**Azure VM 上的 MABS** | Windows Server 2012 R2。<br/><br/> Windows 2016 数据中心。<br/><br/> Windows 2019 数据中心。<br/><br/> 我们建议你在 marketplace 中的映像启动。<br/><br/> 最小 A2 标准带两个内核，3.5 GB 的 RAM。
**Azure VM 上的 DPM** | System Center 2012 R2 Update 3 或更高版本。<br/><br/> 符合 [System Center 要求](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)的 Windows 操作系统。<br/><br/> 我们建议你在 marketplace 中的映像启动。<br/><br/> 最小 A2 标准带两个内核，3.5 GB 的 RAM。
**本地 MABS** | 支持的 64 位操作系统：<br/><br/> MABS v3 和更高版本：Windows Server 2019 (Standard、 Datacenter、 Essentials)。 <br/><br/> MABS v2 和更高版本：Windows Server 2016 (Standard、 Datacenter、 Essentials)。<br/><br/> 所有 MABS 版本： Windows Server 2012 R2。<br/><br/>所有 MABS 版本：Windows Storage Server 2012 R2。
**本地 DPM** | 物理服务器/Hyper-V VM：System Center 2012 SP1 或更高版本。<br/><br/> VMware VM：System Center 2012 R2 Update 5 或更高版本。



## <a name="management-support"></a>管理支持

**问题** | **详细信息**
--- | ---
**安装** | 单一用途的计算机上安装 DPM/MABS。<br/><br/> 请勿在安装 DPM/MABS 域控制器上，具有应用程序服务器角色安装的计算机上、 Microsoft Exchange Server 或 System Center Operations Manager，正在运行的计算机上或在群集节点上。<br/><br/> [查看所有 DPM 系统要求](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)。
**域** | DPM/MABS 应加入到域。 首先安装 DPM/MABS，然后将其加入域。 部署后，不支持将 DPM/MABS 移到新域。
**存储** | 新式备份存储 (MBS) 被支持从 DPM 2016/MABS v2 和更高版本。 MBS 不适用于 MABS v1。
**MABS 升级** | 可以直接安装 MABS v3，或者从 MABS v2 升级到 MABS v3。 [了解详细信息](backup-azure-microsoft-azure-backup.md#upgrade-mabs)。
**移动 MABS** | 如果使用的是 MBS，则支持在保留存储的同时将 MABS 移到新服务器。<br/><br/> 新服务器必须与原始服务器同名。 若要保留相同的存储池，并使用同一个 MABS 数据库来存储数据恢复点，则不能更改服务器名称。<br/><br/> 之所以需要备份 MABS 数据库，是因为需要还原它。


## <a name="mabs-support-on-azure-stack"></a>Azure Stack 上的 MABS 支持

可以在 Azure Stack VM 上部署 MABS，以便可以从单个位置管理 Azure Stack Vm 和工作负荷的备份。

组件  | **详细信息**
--- | ---
**Azure Stack VM 上的 MABS** | 在最小大小为 A2。 我们建议您开始使用 Windows Server 2012 R2 或 Windows Server 2016 的映像从 Azure Marketplace。<br/><br/> 不安装任何其他 MABS VM 上。
**MABS 存储** | MABS vm 使用单独的存储帐户。 MABS 上运行的 MARS 代理需要临时存储区，用于缓存位置，用来存储从云中还原数据。
**MABS 存储池** | MABS 存储池的大小取决于数字和附加到 MABS VM 的磁盘的大小。 每个 Azure Stack VM 大小具有最大磁盘数。 例如，A2 是四个磁盘。
**MABS 保留** | 不能超过 5 天保持本地 MABS 磁盘上的备份的数据。
**MABS 纵向扩展** | 若要纵向扩展部署，可以增加 MABS VM 的大小。 例如，你可以从 A 到 D 系列。<br/><br/> 你还可以确保，你要将数据移走通过备份到 Azure 定期。 如有必要，您可以部署附加的 MABS 服务器。
**MABS 上的.NET framework** | MABS VM 需要.NET Framework 3.3 SP1 或更高版本上安装。
**MABS 域** | MABS VM 必须加入域。 必须由具有管理员特权的域用户在 VM 上安装 MABS。
**Azure Stack VM 数据备份** | 可以备份文件、文件夹和应用。
**支持的备份** | 这些操作系统支持适用于你想要备份的 Vm:<br/><br/> Windows Server 半年频道 (Datacenter、 Enterprise、 Standard)<br/><br/> Windows Server 2016 中，Windows Server 2012 R2、 Windows Server 2008 R2
**Azure Stack Vm 的 SQL Server 支持** | 备份 SQL Server 2016，SQL Server 2014，SQL Server 2012 SP1。<br/><br/> 备份和恢复数据库。
**Azure Stack VM 的 SharePoint 支持** | SharePoint 2016，SharePoint 2013 中，SharePoint 2010。<br/><br/> 备份和恢复场、 数据库、 前端和 web 服务器。
**备份的 VM 的网络要求** | Azure Stack 工作负荷中的所有 Vm 必须属于同一虚拟网络，并属于同一订阅。

## <a name="dpmmabs-networking-support"></a>DPM/MABS 网络支持

### <a name="url-access"></a>URL 访问

DPM 服务器/MABS 需要以下 URL 的访问权限：

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS 与 Azure 备份的连接

需要与 Azure 备份服务建立连接才能正常运行备份，并且 Azure 订阅应处于活动状态。 下表显示了不满足这两项要求时的行为。

**MABS 到 Azure** | **订阅** | **备份/还原**
--- | --- | ---
连续 | 活动 | 备份到 DPM/MABS 磁盘。<br/><br/> 备份到 Azure。<br/><br/> 从磁盘还原。<br/><br/> 从 Azure 还原。
连续 | 已过期/已取消预配 | 不备份到磁盘或 Azure。<br/><br/> 如果订阅已过期，你可以从磁盘或 Azure 中进行还原。<br/><br/> 如果订阅已停用，则无法从磁盘或 Azure 中进行还原。 Azure 恢复点已删除。
有 15 天以上未建立连接 | 活动 | 不备份到磁盘或 Azure。<br/><br/> 可以从磁盘或 Azure 还原。
有 15 天以上未建立连接 | 已过期/已取消预配 | 不备份到磁盘或 Azure。<br/><br/> 如果订阅已过期，你可以从磁盘或 Azure 中进行还原。<br/><br/> 如果订阅已停用，则无法从磁盘或 Azure 中进行还原。 Azure 恢复点已删除。

## <a name="dpmmabs-storage-support"></a>DPM/MABS 存储支持

备份到 DPM/MABS 的数据存储在本地磁盘存储上。

**存储** | **详细信息**
--- | ---
**MBS** | 新式备份存储 (MBS) 被支持从 DPM 2016/MABS v2 和更高版本。 MBS 不适用于 MABS v1。
**Azure VM 上的 MABS 存储** | 数据存储在附加到 DPM/MABS VM 和托管 DPM/MABS 中的 Azure 磁盘上。 要用于 DPM/MABS 存储池的磁盘数受虚拟机大小。<br/><br/> A2 VM：4 个磁盘；A3 VM：8 个磁盘；A4 VM：16 个磁盘，每个磁盘的最大大小为 1 TB。 这将确定可用的总备份存储池。<br/><br/> 可以备份的数据量取决于附加的磁盘数目和大小。
**Azure VM 上的 MABS 数据保留期** | 我们建议您保留 DPM/MABS Azure 磁盘上的一天的数据，并将从 DPM/MABS 备份到更长时间保留在保管库。 这样，可以通过将更大量的数据卸载到 Azure 备份来对其进行保护。


### <a name="modern-backup-storage-mbs"></a>新式备份存储 (MBS)
从 DPM 2016/MABS v2 （在 Windows Server 2016 上运行） 和更高版本，您可以充分利用新式备份存储 (MBS)。

- MBS 备份存储在弹性文件系统 (ReFS) 磁盘中。
- MBS 使用 ReFS 块克隆备份更快和更有效地使用存储空间。
- 在将卷添加到本地 DPM/MABS 存储池时，需要可以通过驱动器号配置它们。 然后，可以在不同的卷上配置工作负荷存储。
- 创建保护组用于将数据备份到 DPM/MABS 时，可以选择要使用的驱动器。 例如，可能会为 SQL 或其他高 IOPS 工作负荷高效的驱动器上存储备份和存储将备份频率较低的性能驱动器的工作负载。


## <a name="supported-backups-to-mabs"></a>MABS 支持的备份

下表汇总了可以从本地计算机和 Azure VM 备份到 MABS 的内容。


**备份** | **版本** | **MABS** | **详细信息** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>（32/64 位） | MABS v3、v2 | 本地。 | 卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。<br/><br/> 卷必须至少为 1 GB，且采用 NTFS。 |
**Windows Server 2016（Datacenter、Standard，不支持 Nano）**<br/><br/> 64/32 位 | MABS v3、v2 | 本地/Azure VM。| 卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> 支持已删除重复数据的卷。 |
**Windows Server 2012 R2（Datacenter 和 Standard）**<br/><br/> 64/32 位 | MABS v3、v2 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> **Azure VMprotection**：卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。 |
**Windows Server 2012 SP1（Datacenter 和 Standard）**<br/><br/> 64/32 位 | MABS v3、v2 <br/><br/> 必须安装 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> **Azure VM 保护**：卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。 |
**Windows 2008 R2 SP1（Standard 和 Enterprise）**<br/><br/> 64/32 位 | 由 MABS v3，v2 支持。<br/><br/> 必须安装 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 | 本地/Azure VM。 |   **本地保护**：卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> **Azure VM 保护**：卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。 |
**Windows 2008 R2（Standard 和 Enterprise）**<br/><br/> 64/32 位 | 对于 MABS v2/v3，OS 必须运行 SP1。 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> **Azure VM 保护**：卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。 |
**Windows Server 2008 SP2**<br/><br/> 64/32 位 | MABS v2 v3 | MABS 部署为 VMware VM 时，支持 MABS v2，v3。<br/><br/> 不支持 Azure VM 上运行的 MABS。 | 卷/共享/文件夹/文件；系统状态/裸机。 |
**Windows Storage Server 2008** | MABS v2 v3 | MABS 作为本地物理服务器/的 HYPER-V VM。 <br/><br/> 不支持 Azure VM 上运行的 MABS。 | 卷/共享/文件夹/文件；系统状态/裸机。
**SQL Server 2017** | MABS v3 | 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。 |
**SQL Server 2016/2016 SP1** | MABS v3、v2 | 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。 |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3、v2 | 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。 |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3、v2 | 本地。 | 备份独立 Exchange 服务器、 DAG 下的数据库。<br/><br/> 恢复邮箱和 DAG 下的邮箱数据库。<br/><br/> 不支持 ReFS。<br/><br/> 备份非共享的磁盘群集。<br/><br/> 备份配置为连续复制的 Exchange 服务器。 |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3、v2 | 本地/Azure VM。 | 备份场，前端 web 服务器。<br/><br/> 恢复场、 数据库、 web 应用、 文件或列表项、 SharePoint 搜索、 前端 web 服务器。<br/><br/> 无法备份的内容数据库使用 SQL Server AlwaysOn 的场。 |
**Windows Server 2016 上的 Hyper-V**<br/><br/> **Windows Server 2008 R2 SP1** | MABS v3、v2 | 本地。 | **Hyper-V 主机上的 MABS 代理**：备份整个 VM 和主机数据文件。 备份使用本地存储的 VM、群集中使用 CSV 存储的 VM，以及使用 SMB 文件服务器存储的 VM。<br/><br/> **来宾 VM 上的 MABS 代理**：备份 VM 上运行的工作负荷。 CSV。<br/><br/> **恢复**：VM、VHD/卷/文件夹/文件的项级恢复。<br/><br/> **Linux VM**：备份 HYPER-V 运行 Windows Server 2012 R2 和更高版本时。 Linux VM 的恢复是针对整个计算机进行的。 |
**VMware VM：vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3、v2 | 本地。 | VMware Vm 备份 Csv、 NFS 和 SAN 存储上。<br/><br/> 恢复整个 VM。<br/><br/> Windows/Linux 备份。<br/><br/> 文件夹/文件的项级恢复（仅适用于 Windows VM）。<br/><br/> 不支持 VMware vApp。<br/><br/> Linux VM 的恢复是针对整个计算机进行的。 |



## <a name="supported-backups-to-dpm"></a>DPM 支持的备份

下表汇总了可以从本地计算机和 Azure VM 备份到 DPM 的内容。



**备份** | **DPM** | **详细信息**
--- | --- | ---
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>（32/64 位） | 仅限本地。<br/><br/> 对于备份使用 DPM 2012 R2 的 Windows 10，我们建议安装[更新 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)。 | 卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。<br/><br/> 卷必须至少为 1 GB，且采用 NTFS。
**Windows Server 2016（Datacenter、Standard，不支持 Nano）**<br/><br/> 64/32 位 | 本地/Azure VM。<br/><br/> 仅限 DPM 2016。| 卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> 支持已删除重复数据的卷。
**Windows Server 2012 R2（Datacenter 和 Standard）**<br/><br/> 64/32 位 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> **Azure VM 保护**：卷/共享/文件夹/文件。<br/><br/> 使用 DPM 2012 R2 和更高版本支持的卷删除了重复数据。
**Windows Server 2012 SP1（Datacenter 和 Standard）**<br/><br/> 64/32 位 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> **Azure VM 保护**：卷/共享/文件夹/文件。<br/><br/> 使用 DPM 2012 R2 和更高版本支持的卷删除了重复数据。
**Windows 2008 R2 SP1（Standard 和 Enterprise）**<br/><br/> 64/32 位 | 本地/Azure VM。<br/><br/> 必须安装 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 |   **本地保护**：卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> **Azure VM 保护**：卷/共享/文件夹/文件。
**Windows 2008 R2（Standard 和 Enterprise）**<br/><br/> 64/32 位 | 本地。<br/><br/> 无法将 DPM 安装为 VMware VM。<br/><br/> 不支持 Azure VM 上运行的 DPM。 | **本地保护**：卷/共享/文件夹/文件；系统状态/裸机。
**Windows Server 2008 SP2**<br/><br/> 64/32 位 | 仅限本地。<br/><br/> 支持作为 VMware VM 运行的 DPM。 不支持作为物理服务器或 Hyper-V VM 运行。 | 卷/共享/文件夹/文件；系统状态/裸机。
**Windows Storage Server 2008** | 在本地作为物理服务器或 Hyper-V VM 运行的 DPM。 | 卷/共享/文件夹/文件；系统状态/裸机。
**SQL Server 2017** | DPM SAC;运行更新汇总 5 或更高版本的 DPM 2016。<br/><br/> 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。
**SQL Server 2016 SP1** | 不支持 DPM 2012 R2；支持 DPM SAC，以及运行更新汇总 4 或更高版本的 DPM 2016。<br/><br/> 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。
**SQL Server 2016** | 不支持 DPM 2012 R2。 对于 DPM SAC，从更新汇总 2 及更高版本的 DPM 2016 支持。<br/><br/> 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | 使用 DPM 2012 R2 运行更新汇总 4 及更高版本的 SQL Server 2014。<br/><br/> 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | 对于 Exchange 2016，DPM 2012 R2 需要更新汇总 9 或更高版本。<br/><br/> 本地 | 备份独立 Exchange 服务器、 DAG 下的数据库。<br/><br/> 恢复邮箱和 DAG 下的邮箱数据库。<br/><br/> 不支持 ReFS。<br/><br/> 备份非共享的磁盘群集。<br/><br/> 备份配置为连续复制的 Exchange 服务器。
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 DPM 2016 及更高版本。<br/><br/>本地/Azure VM。 | 备份场，前端 web 服务器。<br/><br/> 恢复场、 数据库、 web 应用、 文件或列表项、 SharePoint 搜索、 前端 web 服务器。<br/><br/> 无法备份的内容数据库使用 SQL Server AlwaysOn 的场。
**Windows Server 2016 上的 Hyper-V**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 SP1** | HYPER-V 上支持 DPM 2016 2016年及更高版本。<br/><br/> 本地。 | **Hyper-V 主机上的 MABS 代理**：备份整个 VM 和主机数据文件。 备份使用本地存储的 VM、群集中使用 CSV 存储的 VM，以及使用 SMB 文件服务器存储的 VM。<br/><br/> **来宾 VM 上的 MABS 代理**：备份 VM 上运行的工作负荷。 CSV。<br/><br/> **恢复**：VM、VHD/卷/文件夹/文件的项级恢复。<br/><br/> **Linux VM**：备份 HYPER-V 运行 Windows Server 2012 R2 和更高版本时。 Linux VM 的恢复是针对整个计算机进行的。
**VMware VM：vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3、v2 <br/><br/> DPM 2012 R2 需要 System Center 更新汇总 1 <br/><br/>本地。 | VMware Vm 备份 Csv、 NFS 和 SAN 存储上。<br/><br/> 恢复整个 VM。<br/><br/> Windows/Linux 备份。<br/><br/> 文件夹/文件的项级恢复（仅适用于 Windows VM）。<br/><br/> 不支持 VMware vApp。<br/><br/> Linux VM 的恢复是针对整个计算机进行的。


- 群集工作负载由 DPM/MABS 备份应在与 DPM/MABS 相同的域或子/受信任的域。
- 可以使用 NTLM/证书身份验证在不受信任的域或工作组中备份数据。



## <a name="next-steps"></a>后续步骤

- [详细了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 体系结构。
- [查看](backup-support-matrix-mars-agent.md) MARS 代理支持的操作。
- [设置](backup-azure-microsoft-azure-backup.md) MABS 服务器。
- [设置 DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)。
