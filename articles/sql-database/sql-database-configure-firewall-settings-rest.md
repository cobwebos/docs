---
title: "使用 REST API 配置 Azure SQL 数据库服务器级防火墙规则 | Microsoft 文档"
description: "了解如何配置防火墙以允许 IP 地址访问 Azure SQL 数据库。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: cd5d47aa012b21a283e3d6482fe0afddcfa14033


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>使用 REST API 配置 Azure SQL 数据库服务器级防火墙规则
> [!div class="op_single_selector"]
> * [概述](sql-database-firewall-configure.md)
> * [Azure 门户](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Microsoft Azure SQL 数据库使用防火墙规则，以便允许连接到服务器和数据库。 可在 Azure SQL 数据库服务器中为 master 数据库或用户数据库定义服务器级别和数据库级别防火墙设置，从而有选择地允许对数据库的访问。

> [!IMPORTANT]
> 若要允许来自 Azure 的应用程序连接到数据库服务器，则必须启用 Azure 连接。 有关防火墙规则和启用来自 Azure 的连接的详细信息，请参阅 [Azure SQL 数据库防火墙](sql-database-firewall-configure.md)。 如果要在 Azure 云边界内建立连接，可能需要打开一些其他 TCP 端口。 有关详细信息，请参阅[用于 ADO.NET 4.5 和 SQL 数据库 V12 的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)中的 **SQL 数据库 V12：内部与外部**部分。
> 
> 

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>通过 REST API 管理服务器级别防火墙规则
1. 通过 REST API 管理防火墙规则必须进行身份验证。 相关详细信息，请参阅[使用 Azure Resource Manager API 进行授权的开发人员指南](../resource-manager-api-authentication.md)。
2. 可使用 REST API 创建、更新或删除服务器级别规则
   
    若要创建或更新服务器级防火墙规则，请使用以下内容执行 PUT 方法：
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    请求正文
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    若要删除现有服务器级别防火墙规则，请使用以下内容执行 DELETE 方法：

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>使用 REST API 管理防火墙规则
* [创建或更新防火墙规则](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [删除防火墙规则](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [获取防火墙规则](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [列出所有防火墙规则](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>后续步骤
有关如何使用 Transact-SQL 创建服务器级和数据库级防火墙规则的指导文章，请参阅[使用 T-SQL 配置 Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-configure-firewall-settings-tsql.md)。 

有关如何使用其他方式创建服务器级防火墙规则的指导文章，请参阅： 

* [使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings.md)
* [使用 PowerShell 配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings-powershell.md)

有关创建数据库的教程，请参阅[使用 Azure 门户在几分钟内创建一个 SQL 数据库](sql-database-get-started.md)。
有关从开放源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅 [SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要了解如何导航到数据库，请参阅[管理数据库的访问和登录安全](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他资源
* [保护你的数据库](sql-database-security.md)
* [SQL Server 数据库引擎和 Azure SQL 数据库安全中心](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->





<!--HONumber=Dec16_HO2-->


