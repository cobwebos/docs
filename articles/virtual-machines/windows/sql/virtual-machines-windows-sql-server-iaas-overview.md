---
title: "Azure 虚拟机上的 SQL Server 概述 | Microsoft Docs"
description: "了解如何在 Azure 虚拟机上运行完整的 SQL Server 版本。 获取到所有 SQL Server VM 映像和相关内容的直接链接。"
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/09/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 10840ee4ff070436e2c21d51846ea6363825abac
ms.lasthandoff: 04/22/2017


---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上的 SQL Server 概述
本主题介绍在 Azure 虚拟机 (VM) 上运行 SQL Server 的选项，提供了[门户映像链接](#option-1-create-a-sql-vm-with-per-minute-licensing)，同时概述了[常见任务](#manage-your-sql-vm)。

> [!NOTE]
> 如果用户已熟悉 SQL Server，只是想了解如何部署 SQL Server VM，则请参阅[在 Azure 门户中预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。
> 
> 

## <a name="overview"></a>概述
如果用户是数据库管理员或开发人员，则可通过 Azure VM 将本地 SQL Server 工作负荷和应用程序移到云中。 以下视频从技术方面概述了 SQL Server Azure VM。

> [!VIDEO https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016/player]
> 
> 

视频涵盖以下几个方面：

| 时间 | 区域 |
| --- | --- |
| 00:21 |什么是 Azure VM？ |
| 01:45 |“安全” |
| 02:50 |连接 |
| 03:30 |存储可靠性和性能 |
| 05:20 |VM 大小 |
| 05:54 |高可用性和 SLA |
| 07:30 |配置支持 |
| 08:00 |监视 |
| 08:32 |演示：创建 SQL Server 2016 VM |

> [!NOTE]
> 此视频重点介绍 SQL Server 2016，不过 Azure 提供许多 SQL Server 版本的 VM 映像，包括 2008、2012、2014 和 2016。 
> 
> 

## <a name="scenarios"></a>方案
用户选择在 Azure 中托管数据有许多原因。 如果将应用程序转移至 Azure，则同时转移数据会改善性能。 此外还有其他好处。 用户可以自动获得多个数据中心的访问权限，从而获得全局支持和灾难恢复能力。 另外，数据的安全性和持久性也得到了高度保障。

在 Azure VM 中运行 SQL Server 是在 Azure 中存储关系数据的一个选项。 它适用于多种方案。 例如，用户可能需要将 Azure VM 配置为与本地 SQL Server 计算机类似，越类似越好， 或者可能需要在同一数据库服务器上运行其他的应用程序和服务。 如需了解更多方案和考虑事项，可以参考两大主要资源：

* [SQL Server on Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)（Azure 虚拟机中的 SQL Server）概述了在 Azure VM 中使用 SQL Server 的最佳方案。 
* [选择云 SQL Server 选项：Azure VM (IaaS) 中的 Azure SQL (PaaS) 数据库或 SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) 详细比较了在 VM 中运行的 SQL 数据库和 SQL Server。

## <a name="create-a-new-sql-vm"></a>创建新的 SQL VM
以下部分提供了有关 SQL Server 虚拟机库映像到 Azure 门户的直接链接。 根据你所选的映像，可以基于分钟支付 SQL Server 许可费用，或者可以自带许可 (BYOL)。

在以下教程中查找创建新 SQL VM 的分步指南：[在 Azure 门户中预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。 另外，请查看 [SQL Server VM 的性能最佳实践](virtual-machines-windows-sql-performance.md)，其中介绍了如何选择适当的虚拟机大小和预配期间其他可用的功能。

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>选项 1：创建具有每分钟许可的 SQL 虚拟机
下表提供了虚拟机库中最新 SQL Server 映像的矩阵。 单击任何链接，即可开始创建具有指定版本和操作系统的新 SQL VM。 

> [!TIP]
> 若要了解这些映像的 VM 和 SQL 定价，请参阅 [SQL Server Azure VM 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)。

| 版本 | 操作系统 | 版本 |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |

除了此列表，也可使用 SQL Server 版本和操作系统的其他组合。 在 Azure 门户中通过应用商店搜索查找其他映像。 

## <a id="BYOL"></a> 选项 2：使用现有许可创建 SQL VM
你也可以自带许可 (BYOL)。 在此方案中，你只需支付 VM 费用，SQL Server 许可不需要任何额外的费用。 若要使用自己的许可证，请参考下面的 SQL Server 版本和操作系统对照表。 在门户中，这些映像名称带有 **{BYOL}**前缀。

> [!TIP]
> 自带许可证长时间会节省资金，因为可以持续使用生产型工作负荷。 有关详细信息，请参阅 [SQL Server Azure VM 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)。

| 版本 | 操作系统 | 版本 |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

除了此列表，也可使用 SQL Server 版本和操作系统的其他组合。 在 Azure 门户中通过应用商店搜索查找其他映像（搜索“{BYOL} SQL Server”）。

> [!IMPORTANT]
> 若要使用 BYOL VM 映像，必须具有包含 [Azure 上通过软件保障实现的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)的企业协议。 此外，还需要有所要使用的 SQL Server 版本的有效许可证。 必须在预配 VM 的 [10](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) 天内 **向 Microsoft 提供必要的 BYOL 信息** 。 

> [!NOTE]
> 无法更改按分钟付费的 SQL Server VM 的许可模式来使用自己的许可证。 若要使用自己的许可证，必须创建新的 BYOL VM，并将数据库迁移到新 VM。 

## <a name="manage-your-sql-vm"></a>管理 SQL VM
预配 SQL Server VM 之后，有几项可选的管理任务。 在许多方面，完全可以像管理本地 SOL Server 实例一样配置和管理 SQL Server。 但某些任务是 Azure 特有的。 下列各节重点介绍上述某些领域并提供详细信息链接。

### <a name="connect-to-the-vm"></a>连接到 VM
最基本的管理步骤之一是，通过 SQL Server Management Studio (SSMS) 之类的工具连接到 SQL Server VM。 有关如何连接到新 SQL Server VM 的说明，请参阅[连接到 Azure 上的 SQL Server 虚拟机](virtual-machines-windows-sql-connect.md)。

### <a name="migrate-your-data"></a>迁移数据
如果已有数据库，你会想要将该数据库移至新预配的 SQL VM。 有关迁移选项的列表和指导，请参阅 [将数据库迁移到 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。

### <a name="configure-high-availability"></a>配置高可用性
如果你需要高可用性，请考虑配置 SQL Server 可用性组。 这涉及虚拟网络中的多个 Azure VM。 Azure 门户提供了一个模板用于设置此配置。 有关详细信息，请参阅 [在 Azure Resource Manager 虚拟机中配置 AlwaysOn 可用性组](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如果你想要手动配置可用性组和关联的侦听器，请参阅 [在 Azure VM 中配置 AlwaysOn 可用性组](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

有关其他高可用性注意事项，请参阅 [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)。

### <a name="back-up-your-data"></a>备份数据
Azure VM 可以利用 [自动备份](virtual-machines-windows-sql-automated-backup.md)，定期创建数据库到 Blob 存储的备份。 你也可以手动使用此技术。 有关详细信息，请参阅 [使用 Azure 存储空间进行 SQL Server 备份和还原](virtual-machines-windows-use-storage-sql-server-backup-restore.md)。 有关所有备份和还原选项的概述，请参阅 [Azure 虚拟机中 SQL Server 的备份和还原](virtual-machines-windows-sql-backup-recovery.md)。

### <a name="automate-updates"></a>自动更新
Azure VM 可以使用 [自动修补](virtual-machines-windows-sql-automated-patching.md) 来安排维护时段，以便自动安装重要的 Windows 和 SQL Server 更新。

### <a name="customer-experience-improvement-program-ceip"></a>客户体验改善计划 (CEIP)
客户体验改善计划 (CEIP) 默认情况下已启用。 这样会定期将报告发送至 Microsoft，帮助改进 SQL Server。 CEIP 不需要执行管理任务，除非用户想要在预配后将其禁用。 你可以通过远程桌面连接到 VM，以自定义或禁用 CEIP。 然后运行 **SQL Server 错误和使用情况报告** 实用工具。 请按照说明禁用报告功能。 

有关详细信息，请参阅[接受许可条款](https://msdn.microsoft.com/library/ms143343.aspx)主题的“CEIP”部分。 

## <a name="next-steps"></a>后续步骤
[探索学习路径](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) ：Azure 虚拟机上的 SQL Server。

有关定价的问题，请参阅 [SQL Server Azure VM 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)和 [Azure 定价页](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)。 在“OS/软件”列表中选择 SQL Server 的目标版本。 然后，查看不同大小虚拟机的价格。

其他问题？ 请先参阅 [Azure 虚拟机中的 SQL Server 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)。 同时将你的问题或看法添加在任何 SQL VM 主题的底部，以便与 Microsoft 和社区互动。


