---
title: "T-SQL：Azure SQL 数据库防火墙规则 | Microsoft 文档"
description: "了解如何使用 Transact-SQL，针对访问 Azure SQL 数据库的 IP 地址配置服务器级和数据库级防火墙规则。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/07/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 84efba61fbb0248edef3036de702e3a1aa8ae903
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>使用 T-SQL 配置 Azure SQL 数据库服务器级和数据库级防火墙规则

Microsoft Azure SQL 数据库使用防火墙规则，以便允许连接到服务器和数据库。 可在 Azure SQL 数据库服务器中为 master 数据库或用户数据库定义服务器级别和数据库级别防火墙设置，从而有选择地允许对数据库的访问。

> [!IMPORTANT]
> 若要允许来自 Azure 的应用程序连接到数据库服务器，则必须启用 Azure 连接。 有关防火墙规则和启用来自 Azure 的连接的详细信息，请参阅 [Azure SQL 数据库防火墙](sql-database-firewall-configure.md)。 如果要在 Azure 云边界内建立连接，可能需要打开一些其他 TCP 端口。 有关详细信息，请参阅[用于 ADO.NET 4.5 和 SQL 数据库 V12 的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)中的 **SQL 数据库 V12：内部与外部**部分。
> 
> 

## <a name="server-level-firewall-rules"></a>服务器级别防火墙规则
只有 Azure SQL Server 管理员登录名或 Azure Active Directory 管理员才能使用 Transact-SQL 创建服务器级防火墙规则。

1. 启动查询窗口，并使用 SQL Server Management Studio 连接到虚拟 master 数据库。
2. 可以从查询窗口选择、创建、更新或删除服务器级别防火墙规则。
3. 若要创建或更新服务器级别防火墙规则，请执行 `sp_set_firewall_rule` 存储过程。 以下示例启用服务器 Contoso 上一系列 IP 地址。<br/>首先查看已经存在哪些规则。
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    其次，添加防火墙规则。
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    若要删除服务器级别防火墙规则，请执行 sp_delete_firewall_rule 存储过程。 以下示例删除名为 ContosoFirewallRule 的规则。
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   有关这些存储过程的详细信息，请参阅 [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) 和 [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)。

## <a name="database-level-firewall-rules"></a>数据库级别防火墙规则
只有对数据库具备**控制**权限的数据库用户（例如数据库所有者）才能创建数据库级别防火墙规则。

1. 为 IP 地址创建服务器级防火墙后，通过 Azure 门户或 SQL Server Management Studio 启动一个查询窗口。
2. 连接到你要为其创建数据库级别防火墙规则的数据库。
   
    若要创建新的或更新现有的数据库级别防火墙规则，请执行 `sp_set_database_firewall_rule` 存储过程。 以下示例创建名为 ContosoFirewallRule 的新防火墙规则。
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    若要删除现有的数据库级别防火墙规则，请执行 `sp_delete_database_firewall_rule` 存储过程。 以下示例删除名为 ContosoFirewallRule 的规则。
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

有关这些存储过程的详细信息，请参阅 [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) 和 [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx)。

> [!NOTE]
> 有关演示如何使用数据库级防火墙的教程，请参阅 [SQL 身份验证和授权](sql-database-control-access-sql-authentication-get-started.md)。
>


## <a name="next-steps"></a>后续步骤
有关使用其他方式创建服务器级防火墙规则的文章，请参阅： 

* [使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings.md)
* [使用 PowerShell 配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings-powershell.md)
* [使用 REST API 配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings-rest.md)

有关创建数据库的教程，请参阅[你的第一个 Azure SQL 数据库](sql-database-get-started.md)。
有关从开放源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅 [SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要了解如何导航到数据库，请参阅[管理数据库的访问和登录安全](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他资源
* [保护你的数据库](sql-database-security-overview.md)
* [SQL Server 数据库引擎和 Azure SQL 数据库安全中心](https://msdn.microsoft.com/library/bb510589)


