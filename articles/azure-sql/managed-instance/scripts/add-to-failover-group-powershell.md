---
title: PowerShell：将托管实例添加到自动故障转移组
titleSuffix: Azure SQL Managed Instance
description: Azure PowerShell 示例脚本，用于创建托管实例，将其添加到自动故障转移组，然后测试故障转移。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 07/16/2019
ms.openlocfilehash: cd954f07d733e4d80054d6f429a748ca59b5747d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273171"
---
# <a name="use-powershell-to-add-a-managed-instance-to-a-failover-group"></a>使用 PowerShell 将托管实例添加到故障转移组 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

此 PowerShell 脚本示例创建两个托管实例，将其添加到故障转移组，然后测试从主托管实例到辅助托管实例的故障转移。 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 1.4.0 或更高版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="sample-scripts"></a>示例脚本

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。 需要删除资源组两次。 第一次删除资源组将删除托管实例和虚拟群集，但随后会失败，并显示错误消息 `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'`。 再次运行 Remove-AzResourceGroup 命令，删除所有残留资源以及资源组。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建 Azure 资源组。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 创建网络安全组。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 创建路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虚拟网络的子网配置。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 获取网络安全组。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 将网络安全规则配置添加到网络安全组。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新网络安全组。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 将路由添加到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 创建托管实例。  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| 返回有关 Azure SQL 数据库托管实例的详细信息。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 为虚拟网络网关创建 IP 配置 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 创建虚拟网络网关 |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 创建两个虚拟网络网关之间的连接。   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 创建新的 Azure SQL 托管实例故障转移组。  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 获取或列出 SQL 托管实例故障转移组。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 执行 SQL 托管实例故障转移组的故障转移。 | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组。 | 

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

SQL 托管实例的其他 PowerShell 脚本示例可在[ Azure SQL 托管实例 PowerShell 脚本](../../database/powershell-script-content-guide.md)中找到。
