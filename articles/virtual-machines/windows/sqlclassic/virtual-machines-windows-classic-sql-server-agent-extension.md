---
title: "在 SQL VM 上自动完成管理任务（经典）| Microsoft 文档"
description: "本主题介绍如何管理可以自动执行特定 SQL Server 管理任务的 SQL Server 代理扩展。 这些任务包括自动备份、自动修补和 Azure 密钥保管库集成。 本主题使用经典部署模式。"
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30fa9128cd51a7498449c991b58500ad9acdd3d4
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>使用 SQL Server 代理扩展在 Azure 虚拟机上自动完成管理任务（经典）
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [经典](../classic/sql-server-agent-extension.md)
> 
>
 
Azure 虚拟机上运行的 SQL Server IaaS 代理扩展 (SQLIaaSAgent) 可以自动执行管理任务。 本主题概述了该扩展支持的服务以及有关安装、状态及删除的说明。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 若要查看本文中的 Resource Manager 版本，请参阅[适用于 SQL Server VM Resource Manager 的 SQL Server 代理扩展](../sql/virtual-machines-windows-sql-server-agent-extension.md)。

## <a name="supported-services"></a>支持的服务
SQL Server IaaS 代理扩展支持以下管理任务：

| 管理功能 | 说明 |
| --- | --- |
| **SQL 自动备份** |对 VM 中的 SQL Server 默认实例自动执行所有数据库的备份计划。 有关详细信息，请参阅[在 Azure 虚拟机（经典）中对 SQL Server 进行自动备份](../classic/sql-automated-backup.md)。 |
| **SQL 自动修补** |配置维护时段，可在此时段更新 VM，避开工作负荷的高峰期。 有关详细信息，请参阅[在 Azure 虚拟机（经典）中对 SQL Server 进行自动修补](../classic/sql-automated-patching.md)。 |
| **Azure 密钥保管库集成** |可在 SQL Server VM 上自动安装和配置 Azure Key Vault。 有关详细信息，请参阅[在 Azure VM（经典）上配置 SQL Server 的 Azure 密钥保管库集成](../classic/ps-sql-keyvault.md)。 |

## <a name="prerequisites"></a>先决条件
在 VM 上使用 SQL Server IaaS 代理扩展的要求：

### <a name="operating-system"></a>操作系统：
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server 版本：
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell：
[下载和配置最新 Azure PowerShell 命令](/powershell/azure/overview)。

启动 Windows PowerShell，并通过 **Add-AzureAccount** 命令将其连接到 Azure 订阅。

    Add-AzureAccount

如果有多个订阅，请使用 **Select-AzureSubscription** 选择含有目标经典 VM 的订阅。

    Select-AzureSubscription -SubscriptionName <subscriptionname>

此时，通过 **Get-AzureVM** 命令获得一个包含经典虚拟机及其关联服务名称的列表。

    Get-AzureVM

## <a name="installation"></a>安装
对于经典 VM，必须使用 PowerShell 安装 SQL Server IaaS 代理扩展并配置其关联服务。 使用 **Set-AzureVMSqlServerExtension** PowerShell cmdlet 安装扩展。 例如，以下命令在 Windows Server VM（经典）上安装扩展并将其命名为“SQLIaaSExtension”。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

如果更新到最新版本的 SQL IaaS 代理扩展，则必须在更新该扩展后重启虚拟机。

> [!NOTE]
> 经典虚拟机不可通过门户安装和配置 SQL IaaS 代理扩展。
> 
> 

## <a name="status"></a>状态
验证是否已安装扩展的方法之一是在 Azure 门户中查看代理状态。 在虚拟机边栏选项卡中选择“虚拟机”，并单击“扩展”。 随后应看到 **SQLIaaSAgent** 扩展列出。

![Azure 门户中的 SQL Server IaaS 代理扩展](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

也可以使用 **Get-AzureVMSqlServerExtension** Azure Powershell cmdlet。

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>删除
在 Azure 门户中，可以通过单击虚拟机属性的“扩展”边栏选项卡中的省略号来卸载扩展。 然后单击“卸载”。

![在 Azure 门户中卸载 SQL Server IaaS 代理扩展](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

还可使用 **Remove-AzureVMSqlServerExtension** Powershell cmdlet。

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>后续步骤
开始使用扩展支持的服务之一。 有关详细信息，请参阅本文的[支持的服务](#supported-services)部分中提到的主题。

有关在 Azure 虚拟机中运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机中的 SQL Server 概述](../sql/virtual-machines-windows-sql-server-iaas-overview.md)。

