---
title: SQL 数据仓库（门户）中的透明数据加密 | Microsoft 文档
description: SQL 数据仓库中的透明数据加密 (TDE)
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 9c4abb0416acc656a4cfae332377c398260191de
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>SQL 数据仓库中的透明数据加密 (TDE) 入门
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
若要为 SQL 数据仓库启用 TDE，请遵循以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中打开数据库
2. 在数据库边栏选项卡中，单击“设置”按钮
3. 选择“透明数据加密”选项![][1]
4. 选择“打开”设置![][2]
5. 选择“保存”
   ![][3]  

## <a name="disabling-encryption"></a>禁用加密
若要为 SQL 数据仓库禁用 TDE，请遵循以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中打开数据库
2. 在数据库边栏选项卡中，单击“设置”按钮
3. 选择“透明数据加密”选项![][1]
4. 选择“关闭”设置![][4]
5. 选择“保存”
   ![][5]  

## <a name="encryption-dmvs"></a>加密 DMV
可使用下列 DMV 来确认加密：

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
