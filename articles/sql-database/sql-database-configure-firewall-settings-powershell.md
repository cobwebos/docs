---
title: "PowerShell：配置 Azure SQL 数据库防火墙规则 | Microsoft 文档"
description: "了解如何使用 PowerShell，针对访问 Azure SQL 数据库的 IP 地址配置服务器级防火墙规则。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: d80bd1fbb5cdb0492e521a4d600f657fac0e3325


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>使用 PowerShell 配置 Azure SQL 数据库服务器级防火墙规则
> [!div class="op_single_selector"]
> * [概述](sql-database-firewall-configure.md)
> * [Azure 门户](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Azure SQL 数据库使用防火墙规则，以便允许连接到服务器和数据库。 可在 SQL 数据库服务器中为 master 数据库或用户数据库定义服务器级和数据库级防火墙设置，从而有选择地允许对数据库的访问。

> [!IMPORTANT]
> 若要允许来自 Azure 的应用程序连接到数据库服务器，则必须启用 Azure 连接。 有关防火墙规则和启用来自 Azure 的连接的详细信息，请参阅 [Azure SQL 数据库防火墙](sql-database-firewall-configure.md)。 如果要在 Azure 云边界内建立连接，可能需要打开一些其他 TCP 端口。 有关详细信息，请参阅[用于 ADO.NET 4.5 和 SQL 数据库 V12 的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)中的“SQL 数据库 V12：内部与外部”部分。
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>创建服务器防火墙规则
可使用 Azure PowerShell 创建、更新和删除服务器级防火墙规则。

若要创建新的服务器级别防火墙规则，请执行 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) cmdlet。 以下示例启用服务器 Contoso 上一系列 IP 地址。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

若要修改现有服务器级别防火墙规则，请执行 [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx) cmdlet。 以下示例更改名为 ContosoFirewallRule 的规则的可接受 IP 地址的范围。

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -StartIPAddress 192.168.1.4 -EndIPAddress 192.168.1.10 -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'

若要删除现有服务器级别防火墙规则，请执行 [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx) cmdlet。 以下示例删除名为 ContosoFirewallRule 的规则。

    Remove-AzureRmSqlServerFirewallRule -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>使用 PowerShell 管理防火墙规则
还可使用 PowerShell 进行管理。 相关详细信息，请参阅以下主题：

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>后续步骤
若要了解如何使用 Transact-SQL 创建服务器级和数据库级防火墙规则，请参阅[使用 T-SQL 配置 Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-configure-firewall-settings-tsql.md)。

若要了解如何使用其他方式创建服务器级防火墙规则，请参阅：

* [使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings.md)
* [使用 REST API 配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings-rest.md)

有关创建数据库的教程，请参阅[使用 Azure 门户在几分钟内创建一个 SQL 数据库](sql-database-get-started.md)。
有关从开放源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅 [SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要了解如何导航到数据库，请参阅[管理数据库的访问和登录安全](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他资源
* [保护你的数据库](sql-database-security-overview.md)
* [SQL Server 数据库引擎和 Azure SQL 数据库安全中心](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



<!--HONumber=Jan17_HO1-->


