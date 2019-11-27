---
title: PowerShell for DNS 别名
description: PowerShell cmdlet，例如 New-AzSqlServerDNSAlias，可以将新客户端连接重定向到不同的 Azure SQL 数据库服务器，而无需触摸任何客户端配置。
keywords: dns sql database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420407"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>用于管理 Azure SQL 数据库 DNS 别名的 PowerShell

本文提供如何为 Azure SQL 数据库管理 DNS 别名的 PowerShell 脚本。

> [!NOTE]
> 本文已更新为使用 Azure PowerShell Az module 或 Azure CLI。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。
>
> 若要了解有关 Az module 和 AzureRM 兼容性的详细信息，请参阅[Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。 有关安装说明，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)或[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="dns-alias-in-connection-string"></a>连接字符串中的 DNS 别名

若要连接特定的 Azure SQL 数据库服务器，如 SQL Server Management Studio (SSMS) 客户端可以提供的 DNS 别名名称而不是真正的服务器名称。 在下面的示例服务器字符串中，别名 *any 的唯一别名的名称-* 替换四个节点服务器字符串中的第一个圆点分隔节点：

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>先决条件

如果想要运行演示这篇文章中提供的 PowerShell 脚本，适用以下先决条件：

- Azure 订阅和帐户，有关免费试用，请参阅[azure 试用](https://azure.microsoft.com/free/)
- 两个 Azure SQL 数据库服务器

## <a name="example"></a>示例

下面的代码示例首先将文本值分配给几个变量。

若要运行代码，请编辑占位符值以匹配系统中的实际值。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用的 cmdlet 如下所示：

- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)：在 Azure SQL 数据库服务系统中创建 DNS 别名。 别名引用数据库服务器1。
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)：获取并列出分配给 SQL DB 服务器1的所有别名。
- [AzSqlServerDNSAlias：将](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias)别名配置为引用的服务器名称从服务器1修改为服务器2。
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)：使用别名的名称从数据库服务器2中删除别名。

若要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

使用*powershell\_ise*中的 `Get-Module -ListAvailable Az` 查找版本。

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用的命令如下所示：

- [az sql server dns 别名 create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)：在 Azure sql 数据库服务系统中创建 dns 别名。 别名引用数据库服务器1。
- [az sql server dns-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)：获取并列出分配给 sql DB 服务器1的所有别名。
- [az sql server dns-alias 集：将](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias)别名配置为引用的服务器名称从服务器1修改为服务器2。
- [az sql server dns 别名删除](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)：使用别名名称从数据库服务器2中删除别名。

若要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>后续步骤

有关 SQL 数据库的 DNS 别名功能的完整说明，请参阅[AZURE sql 数据库的 dns 别名](dns-alias-overview.md)。
