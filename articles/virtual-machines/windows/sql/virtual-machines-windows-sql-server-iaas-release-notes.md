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
ms.openlocfilehash: e7dea69b507117ba8a3765b5e9bc1fad46be4c53
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444206"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 虚拟机上的 SQL Server 发行说明

Azure 允许你使用内置的 SQL Server 映像部署虚拟机。 本文汇总了 [Azure 虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 的最新版本中的新功能和改进。 本文还列出了不与此版本直接相关但在同一时间范围内发布的值得注意的内容更新。 有关对其他 Azure 服务的改进，请参阅[服务更新](https://azure.microsoft.com/updates)


## <a name="june-2019"></a>2019 年 7 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
| **新 SQL IaaS 安装模式** | 现在可以在[轻型模式下](virtual-machines-windows-sql-server-agent-extension.md)安装 SQL IaaS 扩展, 以避免重新启动 SQL Server 服务。  |
| **修改 SQL Server 版本** | 你现在可以更改 SQL Server VM 的[edition 属性](virtual-machines-windows-sql-change-edition.md)。 |
| **SQL VM RP 更改** | 你可以使用新的 SQL IaaS 模式[向 SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)甚至[Windows 2008 映像](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms)注册 SQL Server VM。 |
| **使用 AHUB 的 BYOL 图像** | 从 marketplace 部署的 BYOL 映像现在可以将其[许可证类型切换为 "PAYG"](virtual-machines-windows-sql-ahb.md#remarks)。| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>2019 年 5 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
| **Azure 门户中的新 SQL VM 管理** | 现在, 可以通过一种新方法来管理 Azure 门户中的 SQL Server VM。 有关详细信息, 请参阅[Azure 门户中的管理 SQL Server VM](virtual-machines-windows-sql-manage-portal.md)。  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>文档改进

| 文档 | 详细信息 |
| --- | --- |
| **新的 SQL VM 门户管理** | 大约有一篇文章已更新为新的 SQL VM 管理门户体验。 | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>2019 年 4 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
| **扩展对 SQL Server 2008/2008R2 的支持** | 通过按*原样迁移到*Azure VM, 扩展对 SQL Server 2008 和 SQL Server 2008 R2 的[支持](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>2019 年 3 月

| 服务改进 | 详细信息 |
| --- | --- |
| **自定义映像可支持性** | 你现在可以将[SQL IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md#installation)安装到自定义 OS 和 sql 映像, 这提供了灵活的[许可](virtual-machines-windows-sql-ahb.md)功能。 将自定义映像注册到 SQL 资源提供程序时, 请将许可证类型指定为 "AHUB", 否则注册将失败。 | 
| **命名实例支持** | 如果已正确卸载默认实例, 现在可以利用带有命名实例的[SQL IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md#installation)。 | 
| **门户增强功能** | 部署 SQL Server VM 的 Azure 门户体验已改进, 以提高可用性。 有关详细信息, 请参阅简短的[快速入门](quickstart-sql-vm-create-portal.md)和部署 SQL Server VM 的操作[方法](virtual-machines-windows-portal-sql-server-provision.md)指南。|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>2019 年 2 月

| 服务改进 | 详细信息 |
| --- | --- |
| **门户改进** | 现在, 可以使用[Azure 门户](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)将 SQL Server VM 的许可模式从即用即付许可更改为自带许可证。|
|**通过 Azure SQL VM CLI 简化 AG 部署** | 现在, 将可用性组部署到 Azure 中的 SQL Server VM 比以往更容易。 通过[AZURE SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) , 可以从命令行和记录时间中创建 WSFC、ILB 和 AG 侦听器。 有关详细信息, 请参阅[使用 AZURE SQL VM CLI 在 AZURE VM 上配置 SQL Server Always On 可用性组](virtual-machines-windows-sql-availability-group-cli.md)。 | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018 年 12 月

| 服务改进 | 详细信息 |
| --- | --- |
| **新的 SQL 群集组资源提供程序** | 新增了一个资源提供程序 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)，用于定义 Windows 故障转移群集的元数据。 将 SQL Server VM 加入到 SqlVirtualMachineGroups，会启动 Windows 故障转移群集服务，并将该 VM 加入到群集。  |
|**使用 Azure 快速入门模板自动设置可用性组部署** |现在，可以使用两个 Azure 快速入门模板创建 Windows 故障转移群集、将 SQL Server VM 加入到该群集、创建侦听器并配置内部负载均衡器。 有关详细信息, 请参阅[使用 Azure 快速入门模板为 AZURE VM 上的 SQL Server 配置 Always On 可用性组](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **SQL VM 资源提供程序的自动注册** | 在本月之后部署的 SQL Server VM 会自动注册到新的 SQL Server 资源提供程序。 在本月之前部署的 SQL Server VM 仍然需要手动注册。 有关详细信息, 请参阅[向 SQL vm 资源提供程序注册现有 SQL vm](virtual-machines-windows-sql-register-with-resource-provider.md)。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| 服务改进 | 详细信息 |
| --- | --- |
| **新的 SQL VM 资源提供程序** |  新增了一个适用于 SQL Server VM 的资源提供程序 (Microsoft.SqlVirtualMachine)，可用于更好地管理 SQL Server VM。 有关注册 VM 的详细信息，请参阅[将现有 SQL VM 注册到新的资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)。 |
|**切换许可模型** | 现在，可以使用 Azure CLI 或 Powershell 在 SQL VM 的“即用即付”模型与“自带许可”模型之间进行切换。 有关详细信息，请参阅[如何更改 SQL VM 的许可模型](virtual-machines-windows-sql-ahb.md)。 | 
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
