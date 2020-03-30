---
title: Azure 虚拟机上的 SQL 服务器的文档更改*微软文档
description: 了解 Azure VM 上 SQL Server 的新功能和改进
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
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201639"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上的 SQL 服务器的文档更改

Azure 允许您部署内置 SQL Server 映像的虚拟机 （VM）。 本文总结了与[Azure 虚拟机上 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)最近版本中的新功能和改进相关的文档更改。 


## <a name="january-2020"></a>2020 年 1 月

| 更改 | 详细信息 |
| --- | --- |
| **Azure 政府支持** | 现在可以将 SQL Server 虚拟机注册到[AZURE 政府](https://azure.microsoft.com/global-infrastructure/government/)云中托管的虚拟机的 SQL VM 资源提供程序。 | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|更改 | 详细信息 |
 --- | --- |
| **Azure 中的免费 DR 副本** | 如果具有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，则可以在 Azure 中为本地 SQL Server 实例托管用于灾难恢复[的免费被动实例](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。 | 
| **批量资源提供程序注册** | 现在，您可以[批量](virtual-machines-windows-sql-bulk-register-with-resource-provider.md)向资源提供程序注册 SQL 虚拟机。 | 
|**性能优化的存储配置** | 现在，您可以在创建新 SQL Server VM 时[完全自定义存储配置](virtual-machines-windows-sql-server-storage-configuration.md#new-vms)。 |
|**FCI 的高级文件共享** | 现在，您可以使用[高级文件共享](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)而不是原始存储空间[直接](virtual-machines-windows-portal-sql-create-failover-cluster.md)方法创建故障转移群集实例。 
| **Azure 专用主机** | 您可以在[Azure 专用主机](virtual-machines-windows-sql-dedicated-host.md)上运行 SQL Server VM。 | 
| **将 SQL VM 移动到不同的区域** | 使用 Azure 站点恢复[将 SQL Server VM 从一个区域迁移到另一个区域](virtual-machines-windows-sql-move-different-region.md)。 |
|  **新的 SQL IaaS 安装模式** | 现在可以在[轻量级模式下](virtual-machines-windows-sql-server-agent-extension.md)安装 SQL Server IaaS 扩展，以避免重新启动 SQL Server 服务。  |
| **SQL 服务器版本修改** | 您现在可以更改 SQL Server VM[的版本属性](virtual-machines-windows-sql-change-edition.md)。 |
| **对 SQL VM 资源提供程序的更改** | 您可以使用新的 SQL IaaS 模式[向 SQL VM 资源提供程序注册 SQL Server VM。](virtual-machines-windows-sql-register-with-resource-provider.md) 此功能包括[Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)映像。|
| **使用 Azure 混合权益自带许可证映像** | 从 Azure 应用商店部署的自带许可证映像现在可以将其[许可证类型切换到即用即付](virtual-machines-windows-sql-ahb.md#remarks)。| 
| **Azure 门户中的新 SQL Server VM 管理** | 现在有一种在 Azure 门户中管理 SQL Server VM 的方法。 有关详细信息，请参阅在[Azure 门户中管理 SQL Server VM。](virtual-machines-windows-sql-manage-portal.md)  | 
| **对 SQL Server 2008/2008 R2 的扩展支持** | 通过*按 Azure* VM 身份迁移来扩展对 SQL Server 2008 和 SQL Server 2008 R2[的支持](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 | 
| **自定义映像支持性** | 现在，您可以将[SQL Server IaaS 扩展安装](virtual-machines-windows-sql-server-agent-extension.md#installation)到自定义操作系统和 SQL 映像，这提供了[灵活的许可](virtual-machines-windows-sql-ahb.md)的有限功能。 向 SQL 资源提供程序注册自定义映像时，请指定许可证类型为"AHUB"。 否则，注册将失败。 | 
| **命名实例可支持性** | 如果默认实例已正确卸载，则现在可以将[SQL Server IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md#installation)与命名实例一起使用。 | 
| **门户增强** | 已对部署 SQL Server VM 的 Azure 门户体验进行了改进，以提高可用性。 有关详细信息，请参阅部署 SQL Server VM 的简短[快速入门](quickstart-sql-vm-create-portal.md)和更全面[的方式指南](virtual-machines-windows-portal-sql-server-provision.md)。|
| **门户改进** | 现在可以使用[Azure 门户](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider)将 SQL Server VM 的许可模式从即用即付更改为自带许可证。|
| **使用 Azure SQL Server VM CLI 简化可用性组部署** | 现在，将可用性组部署到 Azure 中的 SQL Server VM 比以往任何时候都更容易。 可以使用 Azure [CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid)从命令行创建 Windows 故障转移群集、内部负载平衡器和可用性组侦听器。 有关详细信息，请参阅使用[Azure SQL Server VM CLI 为 Azure VM 上的 SQL Server 配置始终打开的可用性组](virtual-machines-windows-sql-availability-group-cli.md)。 | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 更改 | 详细信息 |
| --- | --- |
|  **SQL Server 群集的新资源提供程序** | 新的资源提供程序（Microsoft.SqlVirtualVm/SqlVirtualMachine 组）定义 Windows 故障转移群集的元数据。 将 SQL Server VM 加入*SqlVirtualMachine 组*将引导 Windows 服务器故障转移群集 （WSFC） 服务并将 VM 加入群集。  |
| **使用 Azure 快速入门模板自动设置可用性组部署** |现在可以创建 Windows 故障转移群集、将 SQL Server VM 加入群集、创建侦听器以及使用两个 Azure 快速启动模板配置内部负载均衡器。 有关详细信息，请参阅使用[Azure 快速入门模板为 Azure VM 上的 SQL Server 配置始终打开的可用性组](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **自动注册到 SQL VM 资源提供程序** | 在本月之后部署的 SQL Server VM 会自动注册到新的 SQL Server 资源提供程序。 本月之前部署的 SQL Server VM 仍需要手动注册。 有关详细信息，请参阅在[Azure 中向 SQL VM 资源提供程序注册 SQL Server 虚拟机](virtual-machines-windows-sql-register-with-resource-provider.md)。|
|**新的 SQL VM 资源提供程序** |  新的资源提供程序 （Microsoft.SqlVirtualMachine） 可以更好地管理 SQL Server VM。 有关注册 VM 的详细信息，请参阅[在 Azure 中向 SQL VM 资源提供程序注册 SQL Server 虚拟机](virtual-machines-windows-sql-register-with-resource-provider.md)。 |
|**交换机许可模型** | 现在，可以使用 Azure CLI 或 PowerShell 在 SQL Server VM 的按使用量付费和自带许可证模型之间切换。 有关详细信息，请参阅[如何更改 Azure 中的 SQL Server 虚拟机的许可模型](virtual-machines-windows-sql-ahb.md)。 | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>其他资源

**Windows VM**：

* [Windows VM 上的 SQL 服务器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [设置 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虚拟机上的 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虚拟机上的 SQL Server 的性能最佳实践](virtual-machines-windows-sql-performance.md)
* [Azure 虚拟机上 SQL Server 的应用程序模式和开发策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [预配 SQL Server Linux 虚拟机](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常见问题 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
