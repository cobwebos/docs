---
title: "配置 SQL 数据库服务器级防火墙规则 | Microsoft 文档"
description: "了解如何配置防火墙以允许 IP 地址访问 Azure SQL 服务器。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: ad72adf626fb5f59f239f57ff0158db101017a1b
ms.openlocfilehash: 7925663bd4233e368f54b38c94888ac1b0f5dcab


---
# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则
> [!div class="op_single_selector"]
> * [概述](sql-database-firewall-configure.md)
> * [Azure 门户](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Azure SQL Server 通过防火墙规则允许连接到服务器和数据库。 可在 Azure SQL Server 逻辑服务器中为 master 数据库或用户数据库定义服务器级别和数据库级别的防火墙设置，从而有选择地允许对数据库的访问。 本主题讨论服务器级防火墙规则。

> [!IMPORTANT]
> 若要允许来自 Azure 的应用程序连接到 Azure SQL Server，则必须启用 Azure 连接。 若要了解防火墙规则的工作方式，请参阅[如何配置 Azure SQL 服务器防火墙\-概述](sql-database-firewall-configure.md)。 如果要在 Azure 云边界内建立连接，可能需要打开一些其他 TCP 端口。 有关详细信息，请参阅[用于 ADO.NET 4.5 和 SQL 数据库 V12 的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)中的 **SQL 数据库 V12：内部与外部**部分。
> 
> 

**建议：**如果多个数据库具有相同的访问要求，并且你不想花时间分别配置每个数据库，请使用管理员的服务器级防火墙规则。 Microsoft 建议尽量使用数据库级防火墙规则，以增强安全性并提高数据库的可移植性。

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户管理现有的服务器级别防火墙规则
重复以下步骤来管理服务器级别的防火墙规则。

* 若要添加当前计算机，请单击“添加客户端 IP”。
* 若要添加其他 IP 地址，请键入“规则名称”、“起始 IP 地址”和“结束 IP 地址”。
* 若要修改现有规则，单击规则中的任意字段并修改。
* 若要删除现有规则，将鼠标悬停在该规则上直到“X”出现在行的末尾。 单击“X”删除规则。

单击“保存”以保存更改。

## <a name="next-steps"></a>后续步骤
有关如何使用 Transact-SQL 创建服务器级和数据库级防火墙规则的指导文章，请参阅[使用 T-SQL 配置 Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-configure-firewall-settings-tsql.md)。 

有关如何使用其他方式创建服务器级防火墙规则的指导文章，请参阅： 

* [使用 PowerShell 配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings-powershell.md)
* [使用 REST API 配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings-rest.md)

有关创建数据库的教程，请参阅[使用 Azure 门户在几分钟内创建一个 SQL 数据库](sql-database-get-started.md)。   

若要了解如何创建可以连接到数据库的其他用户，请参阅 [SQL 数据库身份验证和授权：授予访问权限](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他资源
* [保护你的数据库](sql-database-security.md)   
* [SQL Server 数据库引擎和 Azure SQL 数据库安全中心](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Nov16_HO5-->


