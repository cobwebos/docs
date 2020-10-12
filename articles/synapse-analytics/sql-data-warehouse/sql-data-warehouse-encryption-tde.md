---
title: 透明数据加密（门户）
description: Azure Synapse Analytics 中的透明数据加密 (TDE)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: b530b3a049f41a54ab98cc7d1454018cfc990f75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86495663"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的透明数据加密 (TDE) 入门

> [!div class="op_single_selector"]
>
> * [安全性概述](sql-data-warehouse-overview-manage-security.md)
> * [身份验证](sql-data-warehouse-authentication.md)
> * [加密（门户）](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>所需权限

若要启用透明数据加密 (TDE)，用户必须是管理员或 dbmanager 角色的成员。

## <a name="enabling-encryption"></a>启用加密

若要启用 TDE，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)
2. 在数据库边栏选项卡中，单击“设置”按钮 
3. 选择“透明数据加密”  选项![门户设置](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 选择“开”  设置![门户设置“开”](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. 选择“保存”  
   ![门户设置“保存”](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>禁用加密

若要禁用 TDE，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)
2. 在数据库边栏选项卡中，单击“设置”按钮 
3. 选择“透明数据加密”  选项![门户设置](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 选择“关”  设置![门户设置“关”](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. 选择“保存”  
   ![门户设置“保存”2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>加密 DMV

可使用下列 DMV 来确认加密：

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
