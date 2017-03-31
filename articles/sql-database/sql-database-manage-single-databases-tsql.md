---
title: "T-SQL：创建和管理单一 Azure SQL 数据库 | Microsoft 文档"
description: "有关如何使用 Azure 门户创建和管理单一 Azure SQL 数据库的快速参考"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 951c88124219a6962f655b91275aec64c716b1cd
ms.lasthandoff: 03/28/2017


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>使用 Transact-SQL 创建和管理单一 Azure SQL 数据库

可以使用 [Azure 门户](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API 或 C# 创建和管理单一 Azure SQL 数据库。 本主题说明如何使用 Azure 门户。 有关 PowerShell，请参阅[使用 Powershell 创建和管理单一数据库](scripts/sql-database-create-and-configure-database-powershell.md)。 有关 Transact-SQL，请参阅[使用 Transact-SQL 创建和管理单一数据库](sql-database-manage-single-databases-tsql.md)。 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中使用 Transact-SQL 创建 Azure SQL 数据库

若要在 SQL Server Management Studio 中使用 Transact-SQL 创建 SQL 数据库：

1. 在 SQL Server Management Studio 中使用服务器级别主体登录名或是 **dbmanager** 角色成员的登录名连接到 Azure 数据库服务器。 有关登录名的详细信息，请参阅[管理登录名](sql-database-manage-logins.md)。
2. 在对象资源管理器中，打开“数据库”节点、展开“系统数据库”文件夹、右键单击“master”，然后单击“新建查询”。
3. 使用 **CREATE DATABASE** 语句可创建数据库。 有关详细信息，请参阅 [CREATE DATABASE（SQL 数据库）](https://msdn.microsoft.com/library/dn268335.aspx)。 以下语句将创建名为 **myTestDB** 的数据库，并指定它是默认大小上限为 250 GB 的“标准 S0 版本”数据库。
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. 单击“执行”运行查询。
5. 在对象资源管理器中，右键单击“数据库”节点，然后单击“刷新”，以查看对象资源管理器中的新数据库。 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>更改单一数据库的服务层和性能级别
使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 更改数据库最大大小

> [!TIP]
> 有关使用 Transact-SQL 创建数据库的教程，请参阅[创建数据库 - Azure 门户](sql-database-get-started.md)。
>

## <a name="next-steps"></a>后续步骤
* 有关管理工具的概述，请参阅[管理工具概述](sql-database-manage-overview.md)。
* 若要了解如何使用 Azure 门户执行管理任务，请参阅[使用 Azure 门户管理 Azure SQL 数据库](sql-database-manage-portal.md)。
* 若要了解如何使用 PowerShell 执行管理任务，请参阅[使用 PowerShell 管理 Azure SQL 数据库](sql-database-manage-powershell.md)。
* 若要了解如何使用 SQL Server Management Studio 执行管理任务，请参阅 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 有关 SQL 数据库服务的信息，请参阅[什么是 SQL 数据库](sql-database-technical-overview.md)。 
* 有关 Azure 数据库服务器和数据库功能的信息，请参阅[功能](sql-database-features.md)。

