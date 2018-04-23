---
title: 用于管理 Azure SQL DNS 别名的 PowerShell | Microsoft Docs
description: PowerShell cmdlet，例如新建 AzureRMSqlServerDNSAlias，可以将新客户端连接重定向到不同的 Azure SQL 数据库服务器，而无需触摸任何客户端配置。
keywords: dns sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;amagarwa;maboja
ms.author: dmalik
ms.openlocfilehash: 06c27a2fda79afe5a7f4a0e9efdbc51e171bcbda
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>用于管理 Azure SQL 数据库 DNS 别名的 PowerShell

本文提供如何为 Azure SQL 数据库管理 DNS 别名的 PowerShell 脚本。 该脚本运行以下 cmdlet 采用以下操作：


代码示例中使用的 cmdlet 如下：
- [New-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1)：在 Azure SQL 数据库服务系统中创建新的 DNS 别名。 该别名引用 Azure SQL 数据库服务器 1。
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1)：获取并列出分配给 SQL 数据库服务器 1 的所有 DNS 别名。
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1)：修改别名配置为引用的服务器名称，从服务器1 到 SQL 数据库服务器 2。
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1)：使用别名删除 SQL 数据库服务器 2 的 DNS 别名。


**AzureRm.Sql** 模块 5.1.1 和更高版本中添加了上述 PowerShell cmdlet。




#### <a name="dns-alias-in-connection-string"></a>连接字符串中的 DNS 别名

若要连接特定的 Azure SQL 数据库服务器，如 SQL Server Management Studio (SSMS) 客户端可以提供的 DNS 别名名称而不是真正的服务器名称。 在下面的示例服务器字符串中，别名 *any 的唯一别名的名称-*替换四个节点服务器字符串中的第一个圆点分隔节点：
- 服务器字符串示例：`any-unique-alias-name.database.windows.net`。



## <a name="prerequisites"></a>先决条件

如果想要运行演示这篇文章中提供的 PowerShell 脚本，适用以下先决条件：

- Azure 订阅和帐户。 如需免费试用帐户，请单击 [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/]。

- Azure PowerShell 模块，使用 cmdlet **New-AzureRMSqlServerDNSAlias**。
    - 若要进行安装或升级，请参阅[安装 Azure PowerShell 模块][install-azurerm-ps-84p]。
    - 运行 `Get-Module -ListAvailable AzureRM;`powershell\_ise.exe，若要查找版本。

- 两个 Azure SQL 数据库服务器。

## <a name="code-example"></a>代码示例

以下代码示例通过启动的 PowerShell 将文字值分配给多个变量。 若要运行此代码，必须首先编辑所有占位符值以匹配你的系统中的实际值。 或者，还可以只是学习代码。 操作，还提供代码的控制台输出。


```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```


#### <a name="actual-console-output-from-the-powershell-example"></a>PowerShell 示例实际控制台输出

下面的控制台输出已复制并粘贴从实际运行。


```
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.
 
[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount : 

 
[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName    
----------------- ----------         ------------------    
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>> 
```

## <a name="next-steps"></a>后续步骤

有关 SQL 数据库的 DNS 别名功能的完整说明，请参阅 [Azure SQL 数据库的 DNS 别名][dns-alias-overview-37v]。



<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md

