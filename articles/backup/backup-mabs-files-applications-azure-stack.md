---
title: 备份 Azure Stack 文件和应用程序 | Microsoft Docs
description: 使用 Azure 备份将 Azure Stack 文件与应用程序备份和恢复到 Azure Stack 环境。
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 99ac43efa5d3211bbe2d790f28532e682058313c
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075871"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>在 Azure Stack 上备份文件和应用程序
可以使用 Azure 备份保护（或备份）Azure Stack 上的文件和应用程序。 若要备份文件和应用程序，请将 Microsoft Azure 备份服务器安装为 Azure Stack 上运行的虚拟机。 可以保护同一虚拟网络中任何 Azure Stack 服务器上运行的任何应用程序。 安装 Azure 备份服务器后，可添加 Azure 磁盘以增加可用于短期备份数据的本地存储。 Azure 备份服务器将 Azure 存储用于长期保留。

> [!NOTE]
> 尽管 Azure 备份服务器与 System Center Data Protection Manager (DPM) 类似，但不支持将 DPM 与 Azure Stack 配合使用。
>


## <a name="azure-backup-server-protection-matrix"></a>Azure 备份服务器保护矩阵
Azure 备份服务器保护以下 Azure Stack 虚拟机工作负荷。

| 受保护的数据源 | 保护和恢复 |
| --------------------- | ----------------------- |
| Windows Server 半年频道 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| Windows Server 2016 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| Windows Server 2012 - Datacenter/Entprise/Standard | 卷、文件、文件夹 |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| SQL Server 2016 | 数据库 |
| SQL Server 2014 | 数据库 |
| SQL Server 2012 SP1 | 数据库 |
| SharePoint 2013 | 场、数据库、前端、Web 服务器 |
| SharePoint 2010 | 场、数据库、前端、Web 服务器 |


## <a name="install-azure-backup-server"></a>安装 Azure 备份服务器
若要在 Azure Stack 虚拟机上安装 Azure 备份服务器，请参阅[准备使用 Azure 备份服务器备份工作负荷](backup-azure-microsoft-azure-backup.md)一文。 在安装和配置 Azure 备份服务器之前，请注意以下事项：

### <a name="determining-size-of-virtual-machine"></a>确定虚拟机大小
若要在 Azure Stack 虚拟机上运行 Azure 备份服务器，请使用 A2 大小或更大大小。 为了帮助选择虚拟机大小，请下载 [Azure Stack VM 大小计算器](https://www.microsoft.com/download/details.aspx?id=56832)。

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Azure Stack 虚拟机上的虚拟网络
Azure Stack 工作负荷中使用的所有虚拟机都必须属于同一 Azure 虚拟网络和 Azure 订阅。

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>将备份数据存储在本地磁盘和 Azure 中
Azure备份服务器将备份数据存储在附加到虚拟机的 Azure 磁盘上，以便进行操作恢复。 将磁盘和存储空间附加到虚拟机后，Azure 备份服务器将为你管理存储。 备份数据存储量取决于附加到每个 [Azure Stack 虚拟机](../azure-stack/user/azure-stack-storage-overview.md)的磁盘的数量和大小。 Azure Stack VM 的每个大小都有可以附加到虚拟机的最大磁盘数。 例如，A2 是四个磁盘。 A3 是八个磁盘。 A4 是 16 个磁盘。 同样，磁盘的大小和数量决定了整个备份存储池。

> [!IMPORTANT]
> **不**应在 Azure 备份服务器附加的磁盘上保留操作恢复（备份）数据超过 5 天。
>

在 Azure 中存储备份数据会缩减 Azure Stack 上的备份基础结构。 如果数据的存在已超过五天，应将其存储在 Azure 中。

若要将备份数据存储在 Azure 中，请创建或使用恢复服务保管库。 准备备份 Azure 备份服务器工作负荷时，请[配置恢复服务保管库](backup-azure-microsoft-azure-backup.md#recovery-services-vault)。 配置后，每次运行备份作业时，都会在保管库中创建恢复点。 每个恢复服务保管库最多可容纳 9999 个恢复点。 根据所创建的恢复点数量以及保留期限，可以将备份数据保留多年。 例如，可以每月创建恢复点，并将其保留五年。
 
### <a name="using-sql-server"></a>使用 SQL Server
如果要使用远程 SQL Server 作为 Azure 备份服务器数据库，请仅选择运行 SQL Server 的 Azure Stack VM。

### <a name="azure-backup-server-vm-performance"></a>Azure 备份服务器 VM 性能
如果与其他虚拟机共享，存储帐户大小和 IOPS 限制可能会影响 Azure 备份服务器虚拟机性能。 出于这个原因，应该对 Azure 备份服务器虚拟机使用单独的存储帐户。 在 Azure 备份服务器上运行的 Azure 备份代理需要临时存储，以便：
    - 供自己使用（一个缓存位置），
    - 存储从云中还原的数据（本地临时区域）
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>配置 Azure 备份临时磁盘存储
每个 Azure Stack 虚拟机都附带临时磁盘存储，该磁盘存储可作为卷 `D:\` 供用户使用。 可将 Azure 备份所需的本地临时区域配置为位于 `D:\` 中，并且可将缓存位置置于 `C:\` 上。 这样，就不需要从附加到 Azure 备份服务器虚拟机的数据磁盘划分出任何存储。

### <a name="scaling-deployment"></a>扩展部署
如果想要扩展部署，可以使用以下选项：
  - 纵向扩展 - 将 Azure 备份服务器虚拟机的大小从 A 系列增加到 D 系列，并[根据 Azure Stack 虚拟机说明](../azure-stack/user/azure-stack-manage-vm-disks.md)增加本地存储。
  - 卸载数据 - 将旧数据发送到 Azure 备份服务器，并仅保留附加到 Azure 备份服务器的存储上的最新数据。
  - 横向扩展 - 添加更多 Azure 备份服务器来保护工作负荷。

## <a name="see-also"></a>另请参阅
有关使用 Azure 备份服务器保护其他工作负荷的信息，请参阅以下文章之一：
- [备份 SharePoint 场](backup-azure-backup-sharepoint-mabs.md)
- [备份 SQL Server](backup-azure-sql-mabs.md)
