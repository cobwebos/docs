---
title: Microsoft Azure 备份 server & System Center DPM 支持矩阵
description: 本文汇总了使用 Microsoft Azure 备份服务器或 System Center DPM 备份本地和 Azure VM 资源时的 Azure 备份支持。
author: dcurwin
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: dacurwin
manager: carmonm
ms.openlocfilehash: cb3561030cfd86137d7d668ca14db605cc365e86
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969009"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Microsoft Azure 备份 Server 或 System Center DPM 备份的支持矩阵

可以使用[Azure 备份服务](backup-overview.md)来备份本地计算机和工作负荷，以及 azure 虚拟机（vm）。 本文汇总了使用 Microsoft Azure 备份 Server （MABS）或 System Center Data Protection Manager （DPM）和 Azure 备份来备份计算机的支持设置和限制。

## <a name="about-dpmmabs"></a>关于 DPM/MABS

[System CENTER DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807)是一种企业解决方案，用于配置、简化和管理企业计算机和数据的备份和恢复。 它是 [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) 产品套件的一部分。

MABS 是一种服务器产品，可用于备份本地物理服务器、Vm 和正在其上运行的应用。

MABS 基于 System Center DPM，提供类似的功能，但有一些不同之处：

- 无需 System Center 许可证即可运行 MABS。
- 对于 MABS 和 DPM，Azure 提供长期备份存储。 此外，DPM 允许在磁带上备份数据，以作长期存储。 MABS 不提供此功能。
- 您可以使用辅助 DPM 服务器备份主 DPM 服务器。 辅助服务器将保护主服务器数据库及存储在主服务器上的数据源副本。 如果主服务器发生故障，辅助服务器可以继续保护受主服务器保护的工作负荷，直到主服务器再次可用。  MABS 不提供此功能。

可从[Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=57520)下载 MABS。 它可以在本地运行，也可以在 Azure VM 上运行。

DPM 和 MABS 支持备份各种应用、服务器和客户端操作系统。 它们提供多种备份方案：

- 可以使用系统状态或裸机备份在计算机级别进行备份。
- 可以备份特定的卷、共享、文件夹和文件。
- 可以通过使用优化的应用程序设置来备份特定的应用程序。

## <a name="dpmmabs-backup"></a>DPM/MABS 备份

使用 DPM/MABS 和 Azure 备份进行备份的过程如下所示：

1. DPM/MABS 保护代理安装在要备份的每台计算机上。
1. 将计算机和应用备份到 DPM/MABS 上的本地存储。
1. 在 DPM 服务器/MABS 上安装 Microsoft Azure 恢复服务 (MARS) 代理。
1. MARS 代理使用 Azure 备份将 DPM/MABS 磁盘备份到 Azure 中的备份恢复服务保管库。

有关详细信息：

- [详细了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 体系结构。
- [查看](backup-support-matrix-mars-agent.md)MARS 代理支持的内容。

## <a name="supported-scenarios"></a>支持的方案

**方案** | **代理** | 位置
--- | --- | ---
**备份本地计算机/工作负荷** | DPM/MABS 保护代理在要备份的计算机上运行。<br/><br/> DPM/MABS 服务器上的 MARS 代理。<br/> 启用此功能所需的 Microsoft Azure 恢复服务代理或 Azure 备份代理的最低版本为 2.0.8719.0。  | DPM/MABS 必须在本地运行。

## <a name="supported-deployments"></a>支持的部署

DPM/MABS 可以按下表中所述进行部署。

**部署** | **支持** | **详细信息**
--- | --- | ---
**本地部署** | 物理服务器<br/><br/>Hyper-V VM<br/><br/> VMware VM | 如果 DPM/MABS 安装为 VMware VM，则它仅备份在这些 Vm 上运行的 VMware Vm 和工作负荷。
**部署为 Azure Stack VM** | 仅限 MABS | 不能使用 DPM 来备份 Azure Stack VM。
**部署为 Azure VM** | 保护在这些 Vm 上运行的 Azure Vm 和工作负荷 | 在 Azure 中运行的 DPM/MABS 无法备份本地计算机。

## <a name="supported-mabs-and-dpm-operating-systems"></a>支持的 MABS 和 DPM 操作系统

Azure 备份可以备份运行以下任意操作系统的 DPM/MABS 实例。 操作系统应该运行最新的服务包和更新。

**方案** | **DPM/MABS**
--- | ---
**Azure VM 上的 MABS** | Windows Server 2012 R2。<br/><br/> Windows 2016 Datacenter。<br/><br/> Windows 2019 Datacenter。<br/><br/> 建议从 marketplace 开始使用映像。<br/><br/> 最低 A2 标准，具有两个核心和 3.5 GB 的 RAM。
**Azure VM 上的 DPM** | System Center 2012 R2 Update 3 或更高版本。<br/><br/> 符合 [System Center 要求](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)的 Windows 操作系统。<br/><br/> 建议从 marketplace 开始使用映像。<br/><br/> 最低 A2 标准，具有两个核心和 3.5 GB 的 RAM。
**本地 MABS** | 支持的 64 位操作系统：<br/><br/> MABS v3 和更高版本： Windows Server 2019 （Standard、Datacenter、Essentials）。 <br/><br/> MABS v2 和更高版本： Windows Server 2016 （Standard、Datacenter、Essentials）。<br/><br/> 所有 MABS 版本： Windows Server 2012 R2。<br/><br/>所有 MABS 版本： Windows Storage Server 2012 R2。
**本地 DPM** | 物理服务器/Hyper-v VM： System Center 2012 SP1 或更高版本。<br/><br/> VMware VM： System Center 2012 R2 Update 5 或更高版本。

## <a name="management-support"></a>管理支持

**问题** | **详细信息**
--- | ---
**安装** | 在单用途计算机上安装 DPM/MABS。<br/><br/> 不要在安装了应用程序服务器角色的计算机上、在运行 Microsoft Exchange Server 或 System Center Operations Manager 的计算机上或在群集节点上安装 DPM/MABS。<br/><br/> [查看所有 DPM 系统要求](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)。
**域** | DPM/MABS 应加入到域。 首先安装 DPM/MABS，然后将其加入域。 部署后，不支持将 DPM/MABS 移到新域。
**存储** | DPM 2016/MABS v2 和更高版本支持新式备份存储（MB）。 MBS 不适用于 MABS v1。
**MABS 升级** | 可以直接安装 MABS v3，或者从 MABS v2 升级到 MABS v3。 [了解详细信息](backup-azure-microsoft-azure-backup.md#upgrade-mabs)。
**移动 MABS** | 如果使用的是 MBS，则支持在保留存储的同时将 MABS 移到新服务器。<br/><br/> 新服务器必须与原始服务器同名。 若要保留相同的存储池，并使用同一个 MABS 数据库来存储数据恢复点，则不能更改服务器名称。<br/><br/> 之所以需要备份 MABS 数据库，是因为需要还原它。

## <a name="mabs-support-on-azure-stack"></a>Azure Stack 上的 MABS 支持

可以在 Azure Stack VM 上部署 MABS，以便可以从单个位置管理 Azure Stack Vm 和工作负荷的备份。

组件 | **详细信息**
--- | ---
**Azure Stack VM 上的 MABS** | 至少为 A2。 建议从 Azure Marketplace 开始使用 Windows Server 2012 R2 或 Windows Server 2016 映像。<br/><br/> 请勿在 MABS VM 上安装任何其他内容。
**MABS 存储** | 为 MABS VM 使用单独的存储帐户。 在 MABS 上运行的 MARS 代理需要缓存位置的临时存储并保存从云中还原的数据。
**MABS 存储池** | MABS 存储池的大小取决于附加到 MABS VM 的磁盘数量和大小。 每个 Azure Stack VM 大小具有最大磁盘数。 例如，A2 是四个磁盘。
**MABS 保留** | 不要将本地 MABS 磁盘上的备份数据保留五天以上。
**MABS 纵向扩展** | 若要纵向扩展部署，可以增加 MABS VM 的大小。 例如，可以从 A 更改为 D 系列。<br/><br/> 你还可以确保定期将数据卸载到 Azure。 如有必要，可以部署其他 MABS 服务器。
**MABS 上的 .NET Framework** | MABS VM 需要在其上安装 .NET Framework 3.3 SP1 或更高版本。
**MABS 域** | MABS VM 必须加入域。 必须由具有管理员特权的域用户在 VM 上安装 MABS。
**Azure Stack VM 数据备份** | 可以备份文件、文件夹和应用。
**支持的备份** | 对于要备份的 Vm，支持这些操作系统：<br/><br/> Windows Server 半年频道（Datacenter、Enterprise、Standard）<br/><br/> Windows Server 2016，Windows Server 2012 R2，Windows Server 2008 R2
**SQL Server 对 Azure Stack Vm 的支持** | 备份 SQL Server 2016，SQL Server 2014，SQL Server 2012 SP1。<br/><br/> 备份和恢复数据库。
**Azure Stack VM 的 SharePoint 支持** | SharePoint 2016、SharePoint 2013、SharePoint 2010。<br/><br/> 备份和恢复场、数据库、前端和 web 服务器。
**备份的 VM 的网络要求** | Azure Stack 工作负荷中的所有 Vm 都必须属于同一虚拟网络，并且属于同一订阅。

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

**MABS 到 Azure** | 订阅 | **备份/还原**
--- | --- | ---
已连接 | 活动 | 备份到 DPM/MABS 磁盘。<br/><br/> 备份到 Azure。<br/><br/> 从磁盘还原。<br/><br/> 从 Azure 还原。
已连接 | 已过期/已取消预配 | 不备份到磁盘或 Azure。<br/><br/> 如果订阅已过期，你可以从磁盘或 Azure 还原。<br/><br/> 如果取消订阅，则无法从磁盘或 Azure 还原。 Azure 恢复点已删除。
有 15 天以上未建立连接 | 活动 | 不备份到磁盘或 Azure。<br/><br/> 可以从磁盘或 Azure 还原。
有 15 天以上未建立连接 | 已过期/已取消预配 | 不备份到磁盘或 Azure。<br/><br/> 如果订阅已过期，你可以从磁盘或 Azure 还原。<br/><br/> 如果取消订阅，则无法从磁盘或 Azure 还原。 Azure 恢复点已删除。

## <a name="dpmmabs-storage-support"></a>DPM/MABS 存储支持

备份到 DPM/MABS 的数据存储在本地磁盘存储上。

**存储** | **详细信息**
--- | ---
**MBS** | DPM 2016/MABS v2 和更高版本支持新式备份存储（MB）。 MBS 不适用于 MABS v1。
**Azure VM 上的 MABS 存储** | 数据存储在附加到 DPM/MABS VM 的 Azure 磁盘上，并且在 DPM/MABS 中管理。 可用于 DPM/MABS 存储池的磁盘数受 VM 的大小限制。<br/><br/> A2 VM：4个磁盘;A3 VM：8个磁盘;A4 VM：16个磁盘，每个磁盘的最大大小为 1 TB。 这会确定可用的总备份存储池。<br/><br/> 可以备份的数据量取决于附加的磁盘数目和大小。
**Azure VM 上的 MABS 数据保留期** | 建议在 DPM/MABS Azure 磁盘上保留一天的数据，并从 DPM/MABS 备份到保管库进行更长的保留。 这样，可以通过将更大量的数据卸载到 Azure 备份来对其进行保护。

### <a name="modern-backup-storage-mbs"></a>新式备份存储 (MBS)

从 DPM 2016/MABS v2 （在 Windows Server 2016 上运行）和更高版本，你可以利用新式备份存储（MB）。

- MBS 备份存储在弹性文件系统 (ReFS) 磁盘中。
- MB 使用 ReFS 块克隆来实现更快的备份，并更有效地利用存储空间。
- 当你将卷添加到本地 DPM/MABS 存储池时，你可以用驱动器号对它们进行配置。 然后，可以在不同的卷上配置工作负荷存储。
- 创建保护组用于将数据备份到 DPM/MABS 时，可以选择要使用的驱动器。 例如，你可以将 SQL 或其他高 IOPS 工作负荷的备份存储在高性能驱动器上，并存储在较低性能驱动器上不经常备份的工作负载。

## <a name="supported-backups-to-mabs"></a>MABS 支持的备份

下表汇总了可以从本地计算机和 Azure VM 备份到 MABS 的内容。

**备份** | **版本** | **MABS** | **详细信息** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>（32/64 位） | MABS v3、v2 | 本地。 | 卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。<br/><br/> 卷必须至少为 1 GB，且采用 NTFS。 |
**Windows Server 2016（Datacenter、Standard，不支持 Nano）**<br/><br/> 64/32 位 | MABS v3、v2 | 本地/Azure VM。| 卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> 支持已删除重复数据的卷。 |
**Windows Server 2012 R2（Datacenter 和 Standard）**<br/><br/> 64/32 位 | MABS v3、v2 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件;系统状态/裸机。<br/><br/> **Azure VMprotection**：卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。 |
**Windows Server 2012 SP1（Datacenter 和 Standard）**<br/><br/> 64/32 位 | MABS v3、v2 <br/><br/> 必须安装 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件;系统状态/裸机。<br/><br/> **AZURE VM 保护**：卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。 |
**Windows 2008 R2 SP1（Standard 和 Enterprise）**<br/><br/> 64/32 位 | 受 MABS v3，v2 支持。<br/><br/> 必须安装 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 | 本地/Azure VM。 |   **本地保护**：卷/共享/文件夹/文件;系统状态/裸机。<br/><br/> **AZURE VM 保护**：卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。 |
**Windows 2008 R2（Standard 和 Enterprise）**<br/><br/> 64/32 位 | 对于 MABS v3，v2 操作系统必须运行 SP1。 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件;系统状态/裸机。<br/><br/> **AZURE VM 保护**：卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。 |
**Windows Server 2008 SP2**<br/><br/> 64/32 位 | MABS v3、v2 | 当 MABS 部署为 VMware VM 时，支持 MABS v2。<br/><br/> 不支持 Azure VM 上运行的 MABS。 | 卷/共享/文件夹/文件；系统状态/裸机。 |
**Windows Storage Server 2008** | MABS v3、v2 | MABS 作为本地物理服务器/Hyper-v VM。 <br/><br/> 不支持 Azure VM 上运行的 MABS。 | 卷/共享/文件夹/文件；系统状态/裸机。
**SQL Server 2017** | MABS v3 | 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。 |
**SQL Server 2016/2016 SP1** | MABS v3、v2 | 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。 |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3、v2 | 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。 |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3、v2 | 本地。 | 在 DAG 下备份独立的 Exchange server 和数据库。<br/><br/> 恢复邮箱和 DAG 下的邮箱数据库。<br/><br/> 不支持 ReFS。<br/><br/> 备份非共享的磁盘群集。<br/><br/> 备份配置为连续复制的 Exchange 服务器。 |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3、v2 | 本地/Azure VM。 | 备份场，前端 web 服务器。<br/><br/> 恢复场、数据库、web 应用程序、文件或列表项、SharePoint 搜索、前端 web 服务器。<br/><br/> 不能使用内容数据库 SQL Server AlwaysOn 来备份场。 |
**Windows Server 2016 上的 Hyper-V**<br/><br/> **Windows Server 2008 R2 SP1** | MABS v3、v2 | 本地。 | **Hyper-v 主机上的 MABS 代理**：备份整个 vm 和主机数据文件。 备份使用本地存储的 VM、群集中使用 CSV 存储的 VM，以及使用 SMB 文件服务器存储的 VM。<br/><br/> **来宾 VM 上的 MABS 代理**：备份 VM 上运行的工作负荷。 CSV。<br/><br/> **恢复**：虚拟机、项级恢复 VHD/卷/文件夹/文件。<br/><br/> **Linux vm**：在 Windows Server 2012 R2 和更高版本上运行 hyper-v 时进行备份。 Linux VM 的恢复是针对整个计算机进行的。 |
**VMware VM：vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3、v2 | 本地。 | 备份 Csv、NFS 和 SAN 存储上的 VMware Vm。<br/><br/> 恢复整个 VM。<br/><br/> Windows/Linux 备份。<br/><br/> 文件夹/文件的项级恢复（仅适用于 Windows VM）。<br/><br/> 不支持 VMware vApp。<br/><br/> Linux VM 的恢复是针对整个计算机进行的。 |

## <a name="supported-backups-to-dpm"></a>DPM 支持的备份

下表汇总了可以从本地计算机和 Azure VM 备份到 DPM 的内容。

**备份** | **DPM** | **详细信息**
--- | --- | ---
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>（32/64 位） | 仅限本地。<br/><br/> 若要用 DPM 2012 R2 备份 Windows 10，建议安装[Update 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)。 | 卷/共享/文件夹/文件。<br/><br/> 支持已删除重复数据的卷。<br/><br/> 卷必须至少为 1 GB，且采用 NTFS。
**Windows Server 2016（Datacenter、Standard，不支持 Nano）**<br/><br/> 64/32 位 | 本地/Azure VM。<br/><br/> 仅限 DPM 2016。| 卷/共享/文件夹/文件；系统状态/裸机。<br/><br/> 支持已删除重复数据的卷。
**Windows Server 2012 R2（Datacenter 和 Standard）**<br/><br/> 64/32 位 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件;系统状态/裸机。<br/><br/> **AZURE VM 保护**：卷/共享/文件夹/文件。<br/><br/> DPM 2012 R2 及更高版本支持重复数据卷。
**Windows Server 2012 SP1（Datacenter 和 Standard）**<br/><br/> 64/32 位 | 本地/Azure VM。 | **本地保护**：卷/共享/文件夹/文件;系统状态/裸机。<br/><br/> **AZURE VM 保护**：卷/共享/文件夹/文件。<br/><br/> DPM 2012 R2 及更高版本支持重复数据卷。
**Windows 2008 R2 SP1（Standard 和 Enterprise）**<br/><br/> 64/32 位 | 本地/Azure VM。<br/><br/> 必须安装 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 |   **本地保护**：卷/共享/文件夹/文件;系统状态/裸机。<br/><br/> **AZURE VM 保护**：卷/共享/文件夹/文件。
**Windows 2008 R2（Standard 和 Enterprise）**<br/><br/> 64/32 位 | 本地。<br/><br/> 无法将 DPM 安装为 VMware VM。<br/><br/> 不支持 Azure VM 上运行的 DPM。 | **本地保护**：卷/共享/文件夹/文件;系统状态/裸机。
**Windows Server 2008 SP2**<br/><br/> 64/32 位 | 仅限本地。<br/><br/> 支持作为 VMware VM 运行的 DPM。 不支持作为物理服务器或 Hyper-V VM 运行。 | 卷/共享/文件夹/文件；系统状态/裸机。
**Windows Storage Server 2008** | 在本地作为物理服务器或 Hyper-V VM 运行的 DPM。 | 卷/共享/文件夹/文件；系统状态/裸机。
**SQL Server 2017** | DPM SAC;运行更新汇总5或更高版本的 DPM 2016。<br/><br/> 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。
**SQL Server 2016 SP1** | 不支持 DPM 2012 R2；支持 DPM SAC，以及运行更新汇总 4 或更高版本的 DPM 2016。<br/><br/> 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。
**SQL Server 2016** | 不支持 DPM 2012 R2。 支持 DPM SAC、DPM 2016 更新汇总2和更高版本。<br/><br/> 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014，其中 DPM 2012 R2 运行更新汇总4及更高版本。<br/><br/> 本地/Azure VM。| 备份 SQL Server 数据库。<br/><br/> 支持 SQL Server 群集备份。<br/><br/>不支持 CSV 中存储的数据库。
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | 对于 Exchange 2016，DPM 2012 R2 需要更新汇总 9 或更高版本。<br/><br/> 本地 | 在 DAG 下备份独立的 Exchange server 和数据库。<br/><br/> 恢复邮箱和 DAG 下的邮箱数据库。<br/><br/> 不支持 ReFS。<br/><br/> 备份非共享的磁盘群集。<br/><br/> 备份配置为连续复制的 Exchange 服务器。
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | DPM 2016 和更高版本上的 SharePoint 2016。<br/><br/>本地/Azure VM。 | 备份场，前端 web 服务器。<br/><br/> 恢复场、数据库、web 应用程序、文件或列表项、SharePoint 搜索、前端 web 服务器。<br/><br/> 不能使用内容数据库 SQL Server AlwaysOn 来备份场。
**Windows Server 2016 上的 Hyper-V**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 SP1** | DPM 2016 及更高版本支持2016上的 hyper-v。<br/><br/> 本地。 | **Hyper-v 主机上的 MABS 代理**：备份整个 vm 和主机数据文件。 备份使用本地存储的 VM、群集中使用 CSV 存储的 VM，以及使用 SMB 文件服务器存储的 VM。<br/><br/> **来宾 VM 上的 MABS 代理**：备份 VM 上运行的工作负荷。 CSV。<br/><br/> **恢复**：虚拟机、项级恢复 VHD/卷/文件夹/文件。<br/><br/> **Linux vm**：在 Windows Server 2012 R2 和更高版本上运行 hyper-v 时进行备份。 Linux VM 的恢复是针对整个计算机进行的。
**VMware VM：vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3、v2 <br/><br/> DPM 2012 R2 需要 System Center 更新汇总 1 <br/><br/>本地。 | 备份 Csv、NFS 和 SAN 存储上的 VMware Vm。<br/><br/> 恢复整个 VM。<br/><br/> Windows/Linux 备份。<br/><br/> 文件夹/文件的项级恢复（仅适用于 Windows VM）。<br/><br/> 不支持 VMware vApp。<br/><br/> Linux VM 的恢复是针对整个计算机进行的。

- DPM/MABS 备份的群集工作负荷应位于 DPM/MABS 所在的域中，或位于子/受信任的域中。
- 可以使用 NTLM/证书身份验证在不受信任的域或工作组中备份数据。

## <a name="next-steps"></a>后续步骤

- [详细了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 体系结构。
- [查看](backup-support-matrix-mars-agent.md) MARS 代理支持的操作。
- [设置](backup-azure-microsoft-azure-backup.md) MABS 服务器。
- [设置 DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)。
