---
title: Azure VM 上的 SQL Server 发行说明 | Microsoft Docs
description: 了解 Azure VM 上的 SQL Server 的新增功能和改进
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: e3f44181ca1a5ea64815aadf52aa7ea792a21416
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358569"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 虚拟机上的 SQL Server 发行说明

Azure 允许你使用内置的 SQL Server 映像部署虚拟机。 本文列出了在 Azure 虚拟机上部署的 SQL Server 的最新版本中可以预期的新增功能和改进。 

## <a name="december-2018"></a>2018 年 12 月

| **更改** | 详细信息 |
| --- | --- |
| **新的 SQL 群集组资源提供程序** | 新增了一个资源提供程序 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroup)，用于定义与 Windows 故障转移群集相关的元数据。 将 SQL Server VM 加入到 *SqlVirtualMachineGroup*，会启动 Windows 故障转移群集服务，并将该 VM 加入到群集。  |
|**使用 Azure 快速入门模板自动设置可用性组部署** |现在，可以使用两个 Azure 快速入门模板创建 Windows 故障转移群集、将 SQL Server VM 加入到该群集、创建侦听器并配置内部负载均衡器。 有关详细信息，请参阅[使用 Azure 快速入门模板在 SQL Server VM 上为 Always On 可用性组创建 WSFC、侦听器和配置 ILB](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **SQL VM 资源提供程序的自动注册** | 在本月之后部署的 SQL Server VM 会自动注册到新的 SQL Server 资源提供程序。 在本月之前部署的 SQL Server VM 将仍需手动注册。 有关详细信息，请参阅[将现有的 SQL VM 注册到新的资源提供程序](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider)。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| **更改** | 详细信息 |
| --- | --- |
| **新的 SQL VM 资源提供程序** |  新增了一个适用于 SQL Server VM 的资源提供程序 (Microsoft.SqlVirtualMachine)，可用于更好地管理 SQL Server VM。 有关注册 VM 的详细信息，请参阅[将现有 SQL VM 注册到新的资源提供程序](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider)。 |
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
* [设置 SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常见问题 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
