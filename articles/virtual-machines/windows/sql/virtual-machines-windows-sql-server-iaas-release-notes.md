---
title: Azure 虚拟机上 SQL Server 的文档更改 |Microsoft Docs
description: 了解 Azure VM 上 SQL Server 的新增功能和改进
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
ms.date: 08/01/2019
ms.openlocfilehash: c122baa21c5d94b57f29cb0530f0a2655faa87d0
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790373"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上 SQL Server 的文档更改

Azure 允许你使用内置 SQL Server 的映像部署虚拟机（VM）。 本文总结了与[Azure 虚拟机上 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)的最新版本中的新功能和改进相关的文档更改。 


## <a name="october-2019"></a>2019 年 10 月

| 更改 | 详细信息 |
| --- | --- |
| **批量资源提供程序注册** | 你现在可以向资源提供程序[大容量注册](virtual-machines-windows-sql-bulk-register-with-resource-provider.md)SQL 虚拟机。 | 
| **性能优化的存储配置** | 你现在可以在创建新 SQL Server VM 时[完全自定义你的存储配置](virtual-machines-windows-sql-server-storage-configuration.md#new-vms)。 |
| **适用于 FCI 的高级文件共享** | 现在可以使用[高级文件共享](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)（而不是[存储空间直通](virtual-machines-windows-portal-sql-create-failover-cluster.md)的原始方法）创建故障转移群集实例。 
| &nbsp; | &nbsp; |

## <a name="august-2019"></a>2019 年 8 月

| 更改 | 详细信息 |
| --- | --- |
| **Azure 专用主机** | 可以在[Azure 专用主机](virtual-machines-windows-sql-dedicated-host.md)上运行 SQL Server VM。 |
| &nbsp; | &nbsp; |


## <a name="july-2019"></a>2019 年 7 月


| 更改 | 详细信息 |
| --- | --- |
| **将 SQL VM 移到不同的区域** | 使用 Azure Site Recovery 将[SQL Server VM 从一个区域迁移到另一个区域](virtual-machines-windows-sql-move-different-region.md)。 |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>2019 年 7 月


| 更改 | 详细信息 |
| --- | --- |
| **新 SQL IaaS 安装模式** | 现在可以在[轻型模式下](virtual-machines-windows-sql-server-agent-extension.md)安装 SQL Server IaaS 扩展，以避免重新启动 SQL Server 服务。  |
| **修改 SQL Server 版本** | 你现在可以更改 SQL Server VM 的[edition 属性](virtual-machines-windows-sql-change-edition.md)。 |
| **对 SQL VM 资源提供程序的更改** | 你可以使用新的 SQL IaaS 模式[向 SQL VM 资源提供程序注册你的 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) 。 此功能包括[Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)映像。|
| **使用 Azure 混合权益自带许可证映像** | 从 Azure Marketplace 部署的自带许可证现在可以[将其许可证类型切换为即用即付](virtual-machines-windows-sql-ahb.md#remarks)。| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>2019 年 5 月

| 更改 | 详细信息 |
| --- | --- |
| **Azure 门户中的新 SQL Server VM 管理** | 现在有一种方法可以管理 Azure 门户中的 SQL Server VM。 有关详细信息，请参阅[管理 Azure 门户中的 SQL Server vm](virtual-machines-windows-sql-manage-portal.md)。  | 
| &nbsp; | &nbsp; |



## <a name="april-2019"></a>2019 年 4 月

| 更改 | 详细信息 |
| --- | --- |
| **SQL Server 2008/2008 R2 的扩展支持** | 通过按*原样*迁移到 Azure VM，扩展对 SQL Server 2008 和 SQL Server 2008 R2 的[支持](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>2019 年 3 月

| 更改 | 详细信息 |
| --- | --- |
| **自定义映像可支持性** | 你现在可以将[SQL Server IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md#installation)安装到自定义 OS 和 SQL 映像，这提供了灵活的[许可](virtual-machines-windows-sql-ahb.md)功能。 在将自定义映像注册到 SQL 资源提供程序时，请将许可证类型指定为 "AHUB"。 否则，注册将失败。 | 
| **命名实例支持** | 如果已正确卸载默认实例，现在可以将[SQL Server IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md#installation)与命名实例一起使用。 | 
| **门户增强功能** | 部署 SQL Server VM 的 Azure 门户体验已改进，以提高可用性。 有关详细信息，请参阅简短的[快速入门](quickstart-sql-vm-create-portal.md)和部署 SQL Server VM 的操作[方法指南](virtual-machines-windows-portal-sql-server-provision.md)。|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>2019 年 2 月

| 更改 | 详细信息 |
| --- | --- |
| **门户改进** | 现在可以通过使用[Azure 门户](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider)，从即用即付到自带许可证中更改 SQL Server VM 的许可模式。|
|**通过 Azure SQL Server VM CLI 简化可用性组部署** | 现在，将可用性组部署到 Azure 中的 SQL Server VM 比以往更容易。 你可以使用[Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid)从命令行创建 Windows 故障转移群集、内部负载均衡器和可用性组侦听器。 有关详细信息，请参阅[使用 azure SQL SERVER VM CLI 为 AZURE VM 上的 SQL Server 配置 Always On 可用性组](virtual-machines-windows-sql-availability-group-cli.md)。 | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018


### <a name="december-2018"></a>2018 年 12 月

| 更改 | 详细信息 |
| --- | --- |
| **SQL Server 群集的新资源提供程序** | 新资源提供程序（SqlVirtualMachine/SqlVirtualMachineGroups）定义 Windows 故障转移群集的元数据。 将 SQL Server VM 加入到 Windows Server 故障转移群集（WSFC）服务的*SqlVirtualMachineGroups*引导，并将 VM 加入到群集。  |
|**使用 Azure 快速入门模板自动设置可用性组部署** |现在可以创建 Windows 故障转移群集、将 SQL Server Vm 加入其中、创建侦听器，并配置具有两个 Azure 快速入门模板的内部负载均衡器。 有关详细信息，请参阅[使用 azure 快速入门模板为 AZURE VM 上的 SQL Server 配置 Always On 可用性组](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **自动注册到 SQL VM 资源提供程序** | 在本月之后部署的 SQL Server VM 会自动注册到新的 SQL Server 资源提供程序。 此月份之前部署 SQL Server Vm 仍需手动注册。 有关详细信息，请参阅[使用 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机](virtual-machines-windows-sql-register-with-resource-provider.md)。|
| &nbsp; | &nbsp; |


### <a name="november-2018"></a>2018 年 11 月

| 更改 | 详细信息 |
| --- | --- |
| **新的 SQL VM 资源提供程序** |  新的资源提供程序（SqlVirtualMachine）可以更好地管理 SQL Server Vm。 有关注册 Vm 的详细信息，请参阅[使用 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机](virtual-machines-windows-sql-register-with-resource-provider.md)。 |
|**切换许可模型** | 现在，你可以通过使用 Azure CLI 或 PowerShell 在 SQL Server VM 之间切换，并为你的提供自带许可证模型。 有关详细信息，请参阅[如何在 Azure 中更改 SQL Server 虚拟机的许可模式](virtual-machines-windows-sql-ahb.md)。 | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>其他资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [设置 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虚拟机上 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虚拟机上 SQL Server 的性能最佳实践](virtual-machines-windows-sql-performance.md)
* [Azure 虚拟机上 SQL Server 的应用程序模式和开发策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [预配 SQL Server Linux 虚拟机](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常见问题 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
