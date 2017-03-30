---
title: "复制 Azure SQL 数据库 | Microsoft Docs"
description: "创建 Azure SQL 数据库的副本"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e64abfd5581c02b609707f7fa712962c024b293b
ms.lasthandoff: 03/23/2017


---
# <a name="copy-an-azure-sql-database"></a>复制 Azure SQL 数据库

可以在相同或不同的服务器上创建数据库副本。 因此，数据库副本是源数据库截至复制请求发出时的快照。 默认情况下，数据库副本的服务层和性能级别（定价层）都与源数据库相同。 使用 API 时，可以在同一服务层（版本）中选择不同的性能级别。 在完成该复制后，副本将成为能够完全行使功能的独立数据库。 此时，你可以升级或降级到任意版本。 登录名、用户和权限可单独进行管理。  

将某个数据库复制到同一逻辑服务器时，可以在这两个数据库上使用相同的登录名。 用于复制该数据库的安全主体将成为新数据库上的数据库所有者 (DBO)。 所有数据库用户、其权限及安全标识符 (SID) 都复制到数据库副本中。  

将数据库复制到不同的逻辑服务器时，新服务器上的安全主体将成为新数据库上的数据库所有者。 如果你使用[包含的数据库用户](sql-database-manage-logins.md)进行数据访问，请确保主数据库和辅助数据库始终具有相同的用户凭据，这样在复制完成后，你便可以使用相同的凭据立即访问。 如果使用 [Azure Active Directory](../active-directory/active-directory-whatis.md)，则完全无需管理副本中的凭据。 但是，将数据库复制到新服务器时，基于登录名的访问可能不起作用，因为登录名在新服务器上不存在。 若要了解如何在将数据库复制到其他逻辑服务器时管理登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。 

可以使用 [Azure 门户](sql-database-copy-portal.md)、[PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md)  或 [T-SQL](sql-database-copy-transact-sql.md) 复制 SQL 数据库。 

## <a name="next-steps"></a>后续步骤

* 若要了解如何在将数据库复制到其他逻辑服务器时管理用户和登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。
* 有关登录名的信息，请参阅[管理登录名](sql-database-manage-logins.md)
* 若要导出数据库，请参阅[将数据库导出到 BACPAC](sql-database-export.md)

