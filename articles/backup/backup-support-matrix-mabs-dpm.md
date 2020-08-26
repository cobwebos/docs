---
title: MABS 和 System Center DPM 支持矩阵
description: 本文汇总了使用 Microsoft Azure 备份服务器 (MABS) 或 System Center DPM 备份本地和 Azure VM 资源时的 Azure 备份支持。
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 26d9e3012749298a781cd611866bc6d0a6596979
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825216"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>使用 Microsoft Azure 备份服务器或 System Center DPM 进行备份时的支持矩阵

可以使用 [Azure 备份服务](backup-overview.md)备份本地计算机和工作负荷以及 Azure 虚拟机 (VM)。 本文汇总了使用 Microsoft Azure 备份服务器 (MABS) 或 System Center Data Protection Manager (DPM) 和 Azure 备份服务备份计算机时的支持设置与限制。

## <a name="about-dpmmabs"></a>关于 DPM/MABS

[System Center DPM](/system-center/dpm/dpm-overview) 是一个企业解决方案，用于配置、简化及管理企业计算机和数据的备份与恢复。 它是 [System Center](https://www.microsoft.com/system-center/pricing) 产品套件的一部分。

MABS 是可用于备份本地物理服务器、VM 及其上运行的应用的服务器产品。

MABS 基于 System Center DPM，并提供类似的功能，但有几项差别：

- 无需 System Center 许可证即可运行 MABS。
- Azure 为 MABS 和 DPM 提供长期备份存储。 此外，DPM 允许在磁带上备份数据，以作长期存储。 MABS 不提供此功能。
- [您可以使用辅助 dpm 服务器备份主 dpm 服务器](/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019)。 辅助服务器将保护主服务器上存储的主服务器数据库和数据源副本。 如果主服务器出现故障，则辅助服务器可以继续保护由主服务器保护的工作负荷，直到主服务器再次可用为止。  MABS 不提供此功能。

从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57520)下载 MABS。 它可以在本地运行，或者在 Azure VM 上运行。

DPM 和 MABS 支持备份各种应用、服务器和客户端操作系统。 它们提供多种备份方案：

- 可以使用系统状态或裸机备份在计算机级别进行备份。
- 可以备份特定的卷、共享、文件夹和文件。
- 可以使用优化的应用感知设置备份特定的应用。

## <a name="dpmmabs-backup"></a>DPM/MABS 备份

使用 DPM/MABS 和 Azure 备份进行备份的工作原理如下：

1. 在要备份的每台计算机上安装 DPM/MABS 保护代理。
1. 将计算机和应用备份到 DPM/MABS 上的本地存储。
1. 在 DPM 服务器/MABS 上安装 Microsoft Azure 恢复服务 (MARS) 代理。
1. MARS 代理使用 Azure 备份将 DPM/MABS 磁盘备份到 Azure 中的备份恢复服务保管库。

更多相关信息：

- [详细了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 体系结构。
- [查看](backup-support-matrix-mars-agent.md) MARS 代理支持的操作。

## <a name="supported-scenarios"></a>支持的方案

**方案** | **代理** | **位置**
--- | --- | ---
**备份本地计算机/工作负荷** | DPM/MABS 保护代理在要备份的计算机上运行。<br/><br/> DPM/MABS 服务器上的 MARS 代理。<br/> 启用此功能所需的 Microsoft Azure 恢复服务代理或 Azure 备份代理的最低版本为 2.0.8719.0。  | DPM/MABS 必须在本地运行。

## <a name="supported-deployments"></a>支持的部署

可根据下表中的摘要部署 DPM/MABS。

**部署** | **支持** | **详细信息**
--- | --- | ---
**本地部署** | 物理服务器<br/><br/>Hyper-V VM<br/><br/> VMware VM | 如果将 DPM/MABS 安装为 VMware VM，它只会备份 VMware VM，以及这些 VM 上运行的工作负荷。
**部署为 Azure Stack VM** | 仅限 MABS | 不能使用 DPM 来备份 Azure Stack VM。
**部署为 Azure VM** | 保护 Azure VM，以及这些 VM 上运行的工作负荷。 | Azure 中运行的 DPM/MABS 无法备份本地计算机。

## <a name="supported-mabs-and-dpm-operating-systems"></a>支持的 MABS 和 DPM 操作系统

Azure 备份可以备份运行以下任何操作系统的 DPM/MABS 实例。 操作系统应该运行最新的服务包和更新。

**方案** | **DPM/MABS**
--- | ---
**Azure VM 上的 MABS** |  Windows 2016 Datacenter。<br/><br/> Windows 2019 Datacenter。<br/><br/> 我们建议从市场中提供的映像着手。<br/><br/> 具有四个核心和 8 GB RAM 的最小 Standard_A4_v2。
**Azure VM 上的 DPM** | System Center 2012 R2 Update 3 或更高版本。<br/><br/> 符合 [System Center 要求](/system-center/dpm/prepare-environment-for-dpm#dpm-server)的 Windows 操作系统。<br/><br/> 我们建议从市场中提供的映像着手。<br/><br/> 具有四个核心和 8 GB RAM 的最小 Standard_A4_v2。
**本地 MABS** |  MABS v3 和更高版本：Windows Server 2016 或 Windows Server 2019
**本地 DPM** | 物理服务器/Hyper-V VM：System Center 2012 SP1 或更高版本。<br/><br/> VMware VM：System Center 2012 R2 Update 5 或更高版本。

>[!NOTE]
>Windows Server Core 或 Microsoft Hyper-V 服务器不支持安装 Azure 备份服务器。

## <a name="management-support"></a>管理支持

**问题** | **详细信息**
--- | ---
**安装** | 在单一用途计算机上安装 DPM/MABS。<br/><br/> 不要在域控制器、装有应用程序服务器角色的计算机、运行 Microsoft Exchange Server 或 System Center Operations Manager 的计算机或群集节点上安装 DPM/MABS。<br/><br/> [查看](/system-center/dpm/prepare-environment-for-dpm#dpm-server)所有 DPM 系统要求。
**域** | DPM/MABS 应加入域。 首先安装 DPM/MABS，然后将其加入域。 部署后，不支持将 DPM/MABS 移到新域。
**存储** | DPM 2016/MABS v2 和更高版本支持新式备份存储 (MBS)。 MBS 不适用于 MABS v1。
**MABS 升级** | 可以直接安装 MABS v3，或者从 MABS v2 升级到 MABS v3。 [了解详细信息](backup-azure-microsoft-azure-backup.md#upgrade-mabs)。
**移动 MABS** | 如果使用的是 MBS，则支持在保留存储的同时将 MABS 移到新服务器。<br/><br/> 新服务器必须与原始服务器同名。 若要保留相同的存储池，并使用同一个 MABS 数据库来存储数据恢复点，则不能更改服务器名称。<br/><br/> 之所以需要备份 MABS 数据库，是因为需要还原它。

## <a name="mabs-support-on-azure-stack"></a>Azure Stack 上的 MABS 支持

可以在 Azure Stack VM 上部署 MABS，以便可以从单个位置管理 Azure Stack Vm 和工作负荷的备份。

**组件** | **详细信息**
--- | ---
**Azure Stack VM 上的 MABS** | 至少使用 A2 大小。 建议从 Azure Marketplace 开始使用 Windows Server 2012 R2 或 Windows Server 2016 映像。<br/><br/> 不要在 MABS VM 上安装其他任何组件。
**MABS 存储** | 对 MABS VM 使用单独的存储帐户。 MABS 上运行的 MARS 代理需要使用临时存储作为缓存位置，以及保存从云中还原的数据。
**MABS 存储池** | MABS 存储池的大小取决于附加到 MABS VM 的磁盘数目和大小。 每个 Azure Stack VM 大小具有最大磁盘数。 例如，A2 是四个磁盘。
**MABS 保留** | 不要在本地 MABS 磁盘上将备份数据保留 5 天以上。
**MABS 纵向扩展** | 若要纵向扩展部署，可以增加 MABS VM 的大小。 例如，可从 A 系列增大到 D 系列。<br/><br/> 还可以确保通过定期备份到 Azure 来卸载数据。 如有必要，可以部署更多的 MABS 服务器。
**MABS 上的 .NET Framework** | 需要在 MABS VM 上安装 .NET Framework 3.3 SP1 或更高版本。
**MABS 域** | MABS VM 必须加入域。 必须由具有管理员特权的域用户在 VM 上安装 MABS。
**Azure Stack VM 数据备份** | 可以备份文件、文件夹和应用。
**支持的备份** | 要备份的 VM 支持以下操作系统：<br/><br/> Windows Server 半年频道（Datacenter、Enterprise、Standard）<br/><br/> Windows Server 2016、Windows Server 2012 R2、Windows Server 2008 R2
**Azure Stack VM 的 SQL Server 支持** | 备份 SQL Server 2016、SQL Server 2014、SQL Server 2012 SP1。<br/><br/> 备份和恢复数据库。
**Azure Stack VM 的 SharePoint 支持** | SharePoint 2016、SharePoint 2013、SharePoint 2010。<br/><br/> 备份和恢复场、数据库、前端与 Web 服务器。
**备份的 VM 的网络要求** | Azure Stack 工作负荷中的所有 VM 必须在同一个虚拟网络中，并属于同一个订阅。

## <a name="dpmmabs-networking-support"></a>DPM/MABS 网络支持

### <a name="url-access"></a>URL 访问

DPM 服务器/MABS 需要以下 URL 的访问权限：

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Azure ExpressRoute 支持

可以使用公共对等互连（适用于旧线路）和 Microsoft 对等互连通过 Azure ExpressRoute 备份数据。 不支持通过专用对等互连进行备份。

使用公共对等互连：确保访问以下域/地址：

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

使用 Microsoft 对等互连，选择以下服务/区域和相关社区值：

- Azure Active Directory (12076:5060)
- Microsoft Azure 区域（根据恢复服务保管库的位置）
- Azure 存储（根据恢复服务保管库的位置）

有关详细信息，请参阅 [ExpressRoute 路由要求](../expressroute/expressroute-routing.md)。

>[!NOTE]
>对于新线路，公共对等互连已弃用。

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS 与 Azure 备份的连接

需要与 Azure 备份服务建立连接才能正常运行备份，并且 Azure 订阅应处于活动状态。 下表显示了不满足这两项要求时的行为。

**MABS 到 Azure** | **订阅** | **备份/还原**
--- | --- | ---
已连接 | 活动 | 备份到 DPM/MABS 磁盘。<br/><br/> 备份到 Azure。<br/><br/> 从磁盘还原。<br/><br/> 从 Azure 还原。
已连接 | 已过期/已取消预配 | 不备份到磁盘或 Azure。<br/><br/> 如果订阅已过期，则你可以从磁盘或 Azure 还原。<br/><br/> 如果订阅已解除分配，则你无法从磁盘或 Azure 还原。 Azure 恢复点已删除。
有 15 天以上未建立连接 | 活动 | 不备份到磁盘或 Azure。<br/><br/> 可以从磁盘或 Azure 还原。
有 15 天以上未建立连接 | 已过期/已取消预配 | 不备份到磁盘或 Azure。<br/><br/> 如果订阅已过期，则你可以从磁盘或 Azure 还原。<br/><br/> 如果订阅已解除分配，则你无法从磁盘或 Azure 还原。 Azure 恢复点已删除。

## <a name="domain-and-domain-trusts-support"></a>域和域信任支持

|要求 |详细信息 |
|---------|---------|
|Domain    | DPM/MABS 服务器应该在 Windows Server 2019、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 域中。        |
|域信任   |  只要您在单独的林之间建立了林级别的双向信任关系，DPM/MABS 就支持跨林的数据保护。   <BR><BR>   DPM/MABS 可跨域保护服务器和工作站，这些域与 DPM/MABS 服务器域具有双向信任关系的林中。 若要保护工作组或不受信任的域中的计算机，请参阅 [备份和还原工作组和不受信任的域中的工作负荷。](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019)  |

## <a name="dpmmabs-storage-support"></a>DPM/MABS 存储支持

备份到 DPM/MABS 的数据将存储在本地磁盘存储中。

**存储** | **详细信息**
--- | ---
**MBS** | DPM 2016/MABS v2 和更高版本支持新式备份存储 (MBS)。 MBS 不适用于 MABS v1。
**Azure VM 上的 MABS 存储** | 数据存储在附加到 DPM/MABS VM 的 Azure 磁盘上，并在 DPM/MABS 中进行管理。 可用于 DPM/MABS 存储池的磁盘数目受 VM 大小的限制。<br/><br/> A2 VM：4 个磁盘；A3 VM：8 个磁盘；A4 VM：16 个磁盘，每个磁盘的最大大小为 1 TB。 这确定了可用的备份存储池总大小。<br/><br/> 可以备份的数据量取决于附加的磁盘数目和大小。
**Azure VM 上的 MABS 数据保留期** | 我们建议在 DPM/MABS Azure 磁盘上保留数据一天，若要进行长期保留，请将 DPM/MABS 中的数据备份到保管库。 这样，就可以通过将数据卸载到 Azure 备份来保护更多的数据。

### <a name="modern-backup-storage-mbs"></a>新式备份存储 (MBS)

在 DPM 2016/MABS v2（在 Windows Server 2016 上运行）和更高版本中，可以利用新式备份存储 (MBS)。

- MBS 备份存储在弹性文件系统 (ReFS) 磁盘中。
- MBS 使用 ReFS 块克隆，这样可以加快备份速度，并更有效地利用存储空间。
- 将卷添加到本地 DPM/MABS 存储池时，已经为它们配置了驱动器号。 然后，可以在不同的卷上配置工作负荷存储。
- 创建保护组用于将数据备份到 DPM/MABS 时，可以选择要使用的驱动器。 例如，可将 SQL 或其他高 IOPS 工作负荷的备份存储在高性能驱动器中，将不经常备份的工作负荷的备份存储在较低性能的驱动器中。

## <a name="supported-backups-to-mabs"></a>MABS 支持的备份

有关可以使用 Azure 备份服务器保护的各种服务器和工作负载的信息，请参阅 [Azure 备份服务器保护矩阵](./backup-mabs-protection-matrix.md#protection-support-matrix)。

## <a name="supported-backups-to-dpm"></a>DPM 支持的备份

有关可以使用 Data Protection Manager 保护的各种服务器和工作负载的信息，请参阅 [DPM 可以备份的内容](/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019)一文。

- DPM/MABS 备份的群集工作负荷应与 DPM/MABS 位于同一域中，或者在子域/受信任的域中。
- 可以使用 NTLM/证书身份验证在不受信任的域或工作组中备份数据。

## <a name="next-steps"></a>后续步骤

- [详细了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 体系结构。
- [查看](backup-support-matrix-mars-agent.md) MARS 代理支持的操作。
- [设置](backup-azure-microsoft-azure-backup.md) MABS 服务器。
- [设置 DPM](/system-center/dpm/install-dpm)。
