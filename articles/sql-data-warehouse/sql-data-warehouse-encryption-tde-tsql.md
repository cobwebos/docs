---
title: "SQL 数据仓库 (T-SQL) 中的透明数据加密 |Microsoft 文档"
description: "SQL 数据仓库 (T-SQL) 中的透明数据加密 (TDE)"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: barbkess
editor: 
ms.assetid: 8ccefef3-1308-41ee-b336-5e491d1098ae
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: 74c9032aababdce91ed617cd7a4c628915b42504
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>要开始使用透明数据加密 (TDE)
> [!div class="op_single_selector"]
> * [安全性概述](sql-data-warehouse-overview-manage-security.md)
> * [身份验证](sql-data-warehouse-authentication.md)
> * [加密 （门户）](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>所需的权限
若要启用透明数据加密 (TDE)，你必须是管理员或 dbmanager 角色的成员。

## <a name="enabling-encryption"></a>启用加密
请按照下列步骤，若要为 SQL 数据仓库启用 TDE:

1. 连接到*master*承载使用管理员或的成员的登录名的数据库的服务器上的数据库**dbmanager** master 数据库中的角色
2. 执行以下语句对数据库进行加密。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>禁用加密
请按照下列步骤，若要为 SQL 数据仓库禁用 TDE:

1. 连接到*master*数据库使用管理员或的成员的登录名**dbmanager** master 数据库中的角色
2. 执行以下语句对数据库进行加密。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> 必须对 TDE 的设置进行更改前恢复已暂停的 SQL 数据仓库。
> 
> 

## <a name="verifying-encryption"></a>验证加密
若要为 SQL 数据仓库中验证加密状态，请按照以下步骤操作：

1. 连接到*master*或使用管理员或的成员的登录名的实例数据库**dbmanager** master 数据库中的角色
2. 执行以下语句对数据库进行加密。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

结果```1```指示数据库已加密，```0```指示非加密的数据库。

## <a name="encryption-dmvs"></a>加密 Dmv
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
