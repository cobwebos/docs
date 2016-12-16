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
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: 1479595fb7de2e0a37520c7d323624142e624af1


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>使用 Azure 门户创建和管理 Azure SQL 数据库服务器级防火墙规则
> [!div class="op_single_selector"]
> * [概述](sql-database-firewall-configure.md)
> * [Azure 门户](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 

服务器级防火墙规则使管理员可以从指定的 IP 地址或某个范围的 IP 地址访问 SQL 数据库服务器。 当多个数据库具有相同的访问要求，但你不想花时间分别配置每个数据库时，也可以对用户使用服务器级防火墙规则。 Microsoft 建议尽量使用数据库级防火墙规则，以增强安全性并提高数据库的可移植性。 有关 SQL 数据库防火墙的概述，请参阅 [SQL 数据库防火墙规则概述](sql-database-firewall-configure.md)。

> [!Note]
> 有关业务连续性上下文中的可移植数据库的信息，请参阅[灾难恢复的身份验证要求](sql-database-geo-replication-security-config.md)。
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户管理现有的服务器级别防火墙规则
重复以下步骤来管理服务器级别的防火墙规则。

* 若要添加当前计算机，请单击“添加客户端 IP”。
* 若要添加其他 IP 地址，请键入“规则名称”、“起始 IP 地址”和“结束 IP 地址”。
* 若要修改现有规则，单击规则中的任意字段并修改。
* 若要删除现有规则，将鼠标悬停在该规则上直到“X”出现在行的末尾。 单击“X”删除规则。

单击“保存”以保存更改。

## <a name="next-steps"></a>后续步骤

- 有关入门教程，请参阅 [SQL 数据库教程：创建服务器、服务器级防火墙规则、示例数据库、数据库级防火墙规则并连接到 SQL Server](sql-database-get-started.md)。
- 有关安全性入门教程，请参阅[安全性入门](sql-database-get-started-security.md)
- 有关从开放源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅 [SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
- 若要了解如何创建可以连接到数据库的其他用户，请参阅 [SQL 数据库身份验证和授权：授予访问权限](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他资源
* [保护你的数据库](sql-database-security.md)   
* [SQL Server 数据库引擎和 Azure SQL 数据库安全中心](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Dec16_HO2-->


