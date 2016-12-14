---
title: "复制 Azure SQL 数据库 | Microsoft 文档"
description: "创建 Azure SQL 数据库的副本"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sstein; sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e6152f6539962653a582ba4921af82e4447fd76


---
# <a name="copy-an-azure-sql-database"></a>复制 Azure SQL 数据库
> [!div class="op_single_selector"]
> * [概述](sql-database-copy.md)
> * [Azure 门户](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

可以使用 Azure [SQL 数据库自动备份](sql-database-automated-backups.md)创建 SQL 数据库的副本。 数据库副本使用相同的技术作为异地复制功能。 不过，与异地复制不同的是，只要种子设定阶段完成，就会终止复制链接。 因此，复制数据库是源数据库自复制请求发出时起的快照。  
可以在相同或不同的服务器上创建数据库副本。 默认情况下，数据库副本的服务层和性能级别（定价层）都与源数据库相同。 使用 API 时，可以在同一服务层（版本）中选择不同的性能级别。 在完成该复制后，副本将成为能够完全行使功能的独立数据库。 此时，你可以升级或降级到任意版本。 登录名、用户和权限可单独进行管理。  

将某个数据库复制到同一逻辑服务器时，可以在这两个数据库上使用相同的登录名。 用于复制该数据库的安全主体将成为新数据库上的数据库所有者 (DBO)。 所有数据库用户、其权限及安全标识符 (SID) 都复制到数据库副本中。  

将数据库复制到不同的逻辑服务器时，新服务器上的安全主体将成为新数据库上的数据库所有者。 如果你使用[包含的数据库用户](sql-database-manage-logins.md)进行数据访问，请确保主数据库和辅助数据库始终具有相同的用户凭据，这样在复制完成后，你便可以使用相同的凭据立即访问。 如果使用 [Azure Active Directory](../active-directory/active-directory-whatis.md)，则完全无需管理副本中的凭据。 不过，将数据库复制到新服务器时，基于登录名的访问通常不起作用，因为登录名在新服务器上不存在。 若要了解如何在将数据库复制到其他逻辑服务器时管理登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。 

若要复制 SQL 数据库，需要满足以下条件：

* Azure 订阅。 如果你需要 Azure 订阅，只需单击本页顶部的“免费试用”，然后再回来完成本文的相关操作即可。
* 要复制的 SQL 数据库。 如果你没有 SQL 数据库，请按照[创建你的第一个 Azure SQL 数据库](sql-database-get-started.md)文章中的步骤创建一个。

## <a name="next-steps"></a>后续步骤
* 若要使用 Azure 门户复制数据库，请参阅[使用 Azure 门户复制 Azure SQL 数据库](sql-database-copy-portal.md)。
* 若要使用 PowerShell 复制数据库，请参阅[使用 PowerShell 复制 Azure SQL 数据库](sql-database-copy-powershell.md)。
* 若要使用 Transact-SQL 复制数据库，请参阅[使用 Transact-SQL 复制 Azure SQL 数据库](sql-database-copy-transact-sql.md)。
* 若要了解如何在将数据库复制到其他逻辑服务器时管理用户和登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。

## <a name="additional-resources"></a>其他资源
* [管理登录名](sql-database-manage-logins.md)
* [使用 SQL Server Management Studio 连接到 SQL 数据库并执行示例 T-SQL 查询](sql-database-connect-query-ssms.md)
* [将数据库导出到 BACPAC](sql-database-export.md)
* [业务连续性概述](sql-database-business-continuity.md)
* [SQL 数据库文档](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Nov16_HO3-->


