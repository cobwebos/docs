---
title: PowerShell 示例 - 在 Azure SQL 数据库中创建托管实例 | Microsoft Docs
description: Azure PowerShell 示例脚本，用于在 Azure SQL 数据库中创建托管实例
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 32b3c3b45c627d8dfdb42642228a7f9d9786111e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569973"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>使用 PowerShell 创建 Azure SQL 数据库托管实例

此 PowerShell 脚本示例在新虚拟网络的专用子网中创建 Azure SQL 数据库托管实例。 它还为虚拟网络配置路由表和网络安全组。 成功运行脚本后，即可从虚拟网络或本地环境访问托管实例。 请参阅[将 Azure VM 配置为连接到 Azure SQL 数据库托管实例](../sql-database-managed-instance-configure-vm.md)和[配置从本地到 Azure SQL 数据库托管实例的点到站点连接](../sql-database-managed-instance-configure-p2s.md)。

> [!IMPORTANT]
> 有关限制，请参阅[支持的区域](../sql-database-managed-instance-resource-limits.md#supported-regions)和[支持的订阅类型](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 AZ PowerShell 1.4.0 或更高版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | 向虚拟网络添加子网配置 |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | 获取资源组中的虚拟网络 |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | 设置虚拟网络的目标状态 |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | 获取虚拟网络中的子网 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | 配置虚拟网络中子网配置的目标状态 |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | 创建路由表 |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | 获取路由表 |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | 设置路由表的目标状态 |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | 创建 Azure SQL 数据库托管实例 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
