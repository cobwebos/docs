---
title: "SQL 数据库教程：安全性入门"
description: "了解如何创建用户帐户来访问和管理数据库。"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3bd8ac5466ae67df0a1865509c8fb897d1374ec


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>SQL 数据库教程：创建 SQL 数据库用户帐户访问和管理数据库
> [!div class="op_single_selector"]
> * [入门教程](sql-database-get-started-security.md)
> * [授予访问权限](sql-database-manage-logins.md)
> 
> 

在本教程中，了解如何使用 SQL Server Management Studio (SSMS) 执行以下操作：

* 使用服务器级主体登录名登录到 SQL 数据库。
* 创建 SQL 数据库用户帐户。
* 向 SQL 数据库用户授予 [db_owner 权限](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0)。
* 使用非服务器级主体的用户帐户连接到 SQL 数据库。

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>后续步骤
完成本 SQL 数据库教程、创建用户帐户并为该帐户授予 dbo 权限后，便可详细了解 [SQL 数据库安全](sql-database-manage-logins.md)。




<!--HONumber=Nov16_HO2-->


