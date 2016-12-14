---
title: "SQL 数据仓库 (T-SQL) 中的透明数据加密 | Microsoft 文档"
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
ms.date: 10/31/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fb915cd9abb83b10af17046591ce313dcc84fd47


---
# <a name="get-started-with-transparent-data-encryption-tde"></a>透明数据加密 (TDE) 入门
> [!div class="op_single_selector"]
> * [安全性概述](sql-data-warehouse-overview-manage-security.md)
> * [身份验证](sql-data-warehouse-authentication.md)
> * [加密（门户）](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>所需权限
若要启用透明数据加密 (TDE)，用户必须是管理员或 dbmanager 角色的成员。

## <a name="enabling-encryption"></a>启用加密
执行以下步骤，对 SQL 数据仓库启用 TDE：

1. 使用在 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名，连接到托管数据库的服务器上的 *master* 数据库
2. 执行以下语句来加密数据库。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>禁用加密
执行以下步骤，对 SQL 数据仓库禁用 TDE：

1. 使用在 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名，连接到 *master* 数据库
2. 执行以下语句来加密数据库。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> 在更改 TDE 设置之前，必须恢复暂停的 SQL 数据仓库。
> 
> 

## <a name="verifying-encryption"></a>验证加密
若要验证 SQL 数据仓库的加密状态，请遵循以下步骤：

1. 使用在 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名，连接到 *master* 数据库或实例数据库
2. 执行以下语句来加密数据库。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

结果 ```1``` 表示数据库已加密，```0``` 表示数据库未加密。

## <a name="encryption-dmvs"></a>加密 DMV
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[透明数据加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->



<!--HONumber=Nov16_HO3-->


