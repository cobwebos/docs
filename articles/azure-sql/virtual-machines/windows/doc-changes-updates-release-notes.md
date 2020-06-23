---
title: Azure 虚拟机上 SQL Server 的文档更改 | Microsoft Docs
description: 了解 Azure VM 上的 SQL Server 的新增功能和改进
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: f87d72df977e98c60948389d794eb102ac08f8d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032468"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上 SQL Server 的文档更改
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure 允许你使用内置的 SQL Server 映像部署虚拟机 (VM)。 本文汇总了 [Azure 虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 的最新版本中与新功能和改进相关的文档更改。 


## <a name="january-2020"></a>2020 年 1 月

| 更改 | 详细信息 |
| --- | --- |
| **Azure 政府支持** | 现在可以为 [Azure 政府](https://azure.microsoft.com/global-infrastructure/government/)云中托管的虚拟机向 SQL VM 资源提供程序注册 SQL Server 虚拟机。 | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|更改 | 详细信息 |
 --- | --- |
| **Azure 中的免费 DR 副本** | 如果你拥有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，则可以为本地 SQL Server 实例托管一个[免费的被动实例](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)，用于在 Azure 中进行灾难恢复。 | 
| **批量资源提供程序注册** | 现在可将 SQL 虚拟机[批量注册](sql-vm-resource-provider-bulk-register.md)到资源提供程序。 | 
|**性能优化存储配置** | 现在，在创建新的 SQL Server VM 时，可实现[完全自定义存储配置](storage-configuration.md#new-vms)。 |
|**适用于 FCI 的高级文件共享** | 现在可使用[高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)而非[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)的原始方法来创建故障转移群集实例。 
| **Azure 专用主机** | 可在 [Azure 专用主机](dedicated-host.md)上运行 SQL Server VM。 | 
| **将 SQL VM 移到不同的区域** | 使用 Azure Site Recovery [将 SQL Server VM 从一个区域迁移到另一个区域](move-sql-vm-different-region.md)。 |
|  **新 SQL IaaS 安装模式** | 现在可在[轻型模式](sql-server-iaas-agent-extension-automate-management.md)下安装 SQL Server IaaS 扩展，以避免重新启动 SQL Server 服务。  |
| **SQL Server 版本修订** | 现在可以更改 SQL Server VM 的[版本属性](change-sql-server-edition.md)。 |
| **更改 SQL VM 资源提供程序** | 可以使用新的 SQL IaaS 模式[向 SQL VM 资源提供程序注册 SQL Server VM](sql-vm-resource-provider-register.md)。 此功能包括 [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) 映像。|
| **使用 Azure 混合权益的自带许可映像** | 从 Azure 市场部署的自带许可映像现在可以[将其许可证类型切换为即付即用](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)。| 
| **Azure 门户中的新 SQL Server VM 管理** | 现在有一种方法可以管理 Azure 门户中的 SQL Server VM。 有关详细信息，请参阅[在 Azure 门户中管理 SQL Server VM](manage-sql-vm-portal.md)。  | 
| **扩展对 SQL Server 2008/2008 R2 的支持** | 通过“按原样”迁移到 Azure VM，[扩展对 SQL Server 2008 和 SQL Server 2008 R2 的支持](sql-server-2008-extend-end-of-support.md)。 | 
| **自定义映像可支持性** | 现在，可以将 [SQL Server IaaS 扩展](sql-server-iaas-agent-extension-automate-management.md#installation)安装到自定义 OS 和 SQL 映像，这可实现有限的[灵活许可](licensing-model-azure-hybrid-benefit-ahb-change.md)功能。 在向 SQL 资源提供程序注册自定义映像时，请将许可证类型指定为“AHUB”。 否则，注册会失败。 | 
| **命名实例可支持性** | 现在，如果已正确卸载默认实例，可以将 [SQL Server IaaS 扩展](sql-server-iaas-agent-extension-automate-management.md#installation)与命名实例一起使用。 | 
| **门户增强功能** | 改进了 Azure 门户的 SQL Server VM 部署体验，从而提高了可用性。 有关详细信息，请参见简要的[快速入门](sql-vm-create-portal-quickstart.md)和更详尽的[操作指南](create-sql-vm-portal.md)，以部署 SQL Server VM。|
| **门户改进** | 现在，可以使用 [Azure 门户网站](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider)将 SQL Server VM 的许可模式从“即用即付”更改为“自带许可”。|
| **通过 Azure SQL Server VM CLI 简化可用性组部署** | 现在，将可用性组部署到 Azure 中的 SQL Server VM 比以往更加容易。 可以使用 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) 从命令行创建 Windows 故障转移群集、内部负载均衡器和可用性组侦听器。 有关详细信息，请参阅[使用 Azure SQL Server VM CLI 为 Azure VM 上的 SQL Server 配置可用性组](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 更改 | 详细信息 |
| --- | --- |
|  **SQL Server 群集的新资源提供程序** | 新增了一个资源提供程序 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)，用于定义 Windows 故障转移群集的元数据。 将 SQL Server VM 加入到 SqlVirtualMachineGroups，会启动 Windows Server 故障转移群集 (WSFC) 服务，并将该 VM 加入到群集。  |
| **使用 Azure 快速入门模板自动设置可用性组部署** |现在，可以使用两个 Azure 快速入门模板创建 Windows 故障转移群集、将 SQL Server VM 加入到该群集、创建侦听器并配置内部负载均衡器。 有关详细信息，请参阅[使用 Azure 快速启动模板为 Azure VM 上的 SQL Server 配置可用性组](availability-group-quickstart-template-configure.md). | 
| **自动注册到 SQL VM 资源提供程序** | 在本月之后部署的 SQL Server VM 会自动注册到新的 SQL Server 资源提供程序。 在本月之前部署的 SQL Server VM 仍然需要手动注册。 有关详细信息，请参阅[在 Azure 中将 SQL Server 虚拟机注册到 SQL VM 资源提供程序](sql-vm-resource-provider-register.md)。|
|**新的 SQL VM 资源提供程序** |  新增了一个资源提供程序 (Microsoft.SqlVirtualMachine)，可用于更好地管理 SQL Server VM。 有关注册 VM 的详细信息，请参阅[在 Azure 中将 SQL Server 虚拟机注册到 SQL VM 资源提供程序](sql-vm-resource-provider-register.md)。 |
|**切换许可模型** | 现在，可以使用 Azure CLI 或 Powershell 在 SQL Server VM 的“即用即付”模型与“自带许可”模型之间进行切换。 有关详细信息，请参阅[如何在 Azure 中更改 SQL Server 虚拟机的许可模型](licensing-model-azure-hybrid-benefit-ahb-change.md)。 | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>其他资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [设置 SQL Server Windows VM](create-sql-vm-portal.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 虚拟机中 SQL Server 的性能最佳做法](performance-guidelines-best-practices.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](application-patterns-development-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [预配 SQL Server Linux 虚拟机](../linux/sql-vm-create-portal-quickstart.md)
* [常见问题 (Linux)](../linux/frequently-asked-questions-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
