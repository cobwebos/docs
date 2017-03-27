---
title: "Azure 门户：Azure SQL 数据库服务器级防火墙规则 | Microsoft 文档"
description: "了解如何使用 Azure 门户，针对访问 Azure SQL Server 的 IP 地址配置服务器级防火墙规则。"
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
ms.topic: article
ms.date: 03/21/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 1939b69ee806d9091a21f3b21a276175d5a19a62
ms.lasthandoff: 02/17/2017


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>使用 Azure 门户创建和管理 Azure SQL 数据库服务器级防火墙规则

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

- 有关预配和连接到使用服务器级防火墙的服务器的教程，请参阅[教程：使用 Azure 门户与 SQL Server Management Studio 来预配和访问 Azure SQL 数据库](sql-database-get-started.md)。
- 有关 SQL Server 身份验证和数据库级防火墙的教程，请参阅 [SQL 身份验证和授权](sql-database-control-access-sql-authentication-get-started.md)
- 有关从开放源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅 [SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
- 若要了解如何创建可以连接到数据库的其他用户，请参阅 [SQL 数据库身份验证和授权：授予访问权限](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他资源
* [保护你的数据库](sql-database-security-overview.md)   
* [SQL Server 数据库引擎和 Azure SQL 数据库安全中心](https://msdn.microsoft.com/library/bb510589)   




