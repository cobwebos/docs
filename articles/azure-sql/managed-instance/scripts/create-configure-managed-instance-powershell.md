---
title: PowerShell：创建托管实例
titleSuffix: Azure SQL Managed Instance
description: 本文提供用于创建托管实例的 Azure PowerShell 示例脚本。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323685"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>使用 PowerShell 创建托管实例

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

此 PowerShell 脚本示例在新虚拟网络的专用子网中创建托管实例。 它还为虚拟网络配置路由表和网络安全组。 成功运行脚本后，即可从虚拟网络或本地环境访问托管实例。 请参阅[将 Azure VM 配置为连接到 Azure SQL 数据库托管实例](../connect-vm-instance-configure.md)和[配置从本地到 Azure SQL 托管实例的点到站点连接](../point-to-site-p2s-configure.md)。

> [!IMPORTANT]
> 有关限制，请参阅[支持的区域](../resource-limits.md#supported-regions)和[支持的订阅类型](../resource-limits.md#supported-subscription-types)。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 1.4.0 或更高版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令中的某些命令。 有关下表中使用的命令和其他命令的详细信息，请单击命令特定文档的链接。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | 向虚拟网络添加子网配置。 |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | 获取资源组中的虚拟网络。 |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | 设置虚拟网络的目标状态。 |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | 获取虚拟网络中的子网。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | 配置虚拟网络中子网配置的目标状态。 |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | 创建路由表。 |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | 获取路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | 设置路由表的目标状态。 |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | 创建托管实例。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

Azure SQL 托管实例的其他 PowerShell 脚本示例可在 [Azure SQL 托管实例 PowerShell 脚本](../../database/powershell-script-content-guide.md)中找到。
