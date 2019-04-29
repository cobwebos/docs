---
title: Azure VM 上的 SQL Server 发行说明 | Microsoft Docs
description: 了解 Azure VM 上的 SQL Server 的新增功能和改进
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: 23e072369aa8ac6ca6ada5ec185df1a8d7e03c5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477172"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 虚拟机上的 SQL Server 发行说明

Azure 允许你使用内置的 SQL Server 映像部署虚拟机。 本文汇总了 [Azure 虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 的最新版本中的新功能和改进。 本文还列出了不与此版本直接相关但在同一时间范围内发布的值得注意的内容更新。 有关对其他 Azure 服务的改进，请参阅[服务更新](https://azure.microsoft.com/updates)

## <a name="march-2019"></a>2019 年 3 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
| **自定义映像可支持性** | 现在可以安装[SQL IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md#installation)为自定义 OS 和 SQL 映像提供了有限的功能[灵活的许可](virtual-machines-windows-sql-ahb.md)。 当向 SQL 资源提供程序，注册自定义映像指定许可证类型为 AHUB 作为否则注册将失败。  | 
| **命名的实例可支持性** | 现可利用[SQL IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md#installation)使用命名实例，如果已正确卸载默认实例。 | 
| **门户增强功能** | 已改进部署 SQL Server VM 的 Azure 门户体验，以提高可用性。 有关详细信息，请参阅 brief[快速入门](quickstart-sql-vm-create-portal.md)和更全面[操作指南](virtual-machines-windows-portal-sql-server-provision.md)部署 SQL Server VM 的指南。 |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>文档改进

无


## <a name="february-2019"></a>2019 年 2 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
| **门户改进** | 现可从即用即付自带的自己的许可证使用 SQL Server VM 的更改的许可模式[Azure 门户](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1)。|
|**可用性组部署简化使用 Azure SQL VM CLI** | 它是现在比以往要请将可用性组部署到 Azure 中的 SQL Server VM。 [Azure SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) ，可从命令行中，所有和记录时间中创建了 WSFC，ILB 和 AG 侦听器 ！ 有关详细信息，请参阅[使用 Azure SQL VM CLI 为 Azure VM 上的 SQL Server 中配置 Always On 可用性组](virtual-machines-windows-sql-availability-group-cli.md)。 | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018 年 12 月

| 服务改进 | 详细信息 |
| --- | --- |
| **新的 SQL 群集组资源提供程序** | 新增了一个资源提供程序 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)，用于定义 Windows 故障转移群集的元数据。 将 SQL Server VM 加入到 SqlVirtualMachineGroups，会启动 Windows 故障转移群集服务，并将该 VM 加入到群集。  |
|**使用 Azure 快速入门模板自动设置可用性组部署** |现在，可以使用两个 Azure 快速入门模板创建 Windows 故障转移群集、将 SQL Server VM 加入到该群集、创建侦听器并配置内部负载均衡器。 有关详细信息，请参阅[使用 Azure 快速入门模板为 Azure VM 上的 SQL Server 中配置 Always On 可用性组](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **SQL VM 资源提供程序的自动注册** | 在本月之后部署的 SQL Server VM 会自动注册到新的 SQL Server 资源提供程序。 在本月之前部署的 SQL Server VM 仍然需要手动注册。 有关详细信息，请参阅[使用 SQL VM 资源提供程序注册现有的 SQL VM](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| 服务改进 | 详细信息 |
| --- | --- |
| **新的 SQL VM 资源提供程序** |  新增了一个适用于 SQL Server VM 的资源提供程序 (Microsoft.SqlVirtualMachine)，可用于更好地管理 SQL Server VM。 有关注册 VM 的详细信息，请参阅[将现有 SQL VM 注册到新的资源提供程序](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。 |
|**切换许可模型** |现在，可以使用 Azure CLI 或 Powershell 在 SQL VM 的“即用即付”模型与“自带许可”模型之间进行切换。 有关详细信息，请参阅[如何更改 SQL VM 的许可模型](virtual-machines-windows-sql-ahb.md)。 | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>其他资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。
* [设置 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虚拟机中 SQL Server 的性能最佳实践](virtual-machines-windows-sql-performance.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [预配 SQL Server Linux 虚拟机](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常见问题 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
