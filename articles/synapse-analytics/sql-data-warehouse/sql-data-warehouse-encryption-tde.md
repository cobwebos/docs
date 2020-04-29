---
title: 透明数据加密（门户）
description: Azure Synapse Analytics 中的透明数据加密（TDE）
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745228"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>透明数据加密 (TDE) 入门

> [!div class="op_single_selector"]
>
> * [安全概述](sql-data-warehouse-overview-manage-security.md)
> * [身份验证](sql-data-warehouse-authentication.md)
> * [加密（门户）](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>所需的权限

若要启用透明数据加密 (TDE)，用户必须是管理员或 dbmanager 角色的成员。

## <a name="enabling-encryption"></a>启用加密

若要启用 TDE，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中打开数据库
2. 在数据库边栏选项卡中，单击“设置”**** 按钮
3. 选择 "**透明数据加密**" ![选项门户设置](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 选择 "**在**上![设置门户网站设置"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. 选择**保存**
   ![门户设置保存](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>禁用加密

若要禁用 TDE，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中打开数据库
2. 在数据库边栏选项卡中，单击“设置”**** 按钮
3. 选择 "**透明数据加密**" ![选项门户设置](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 选择**关闭**设置![门户设置](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. 选择**保存**
   ![门户设置保存2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>加密 DMV

可使用下列 DMV 来确认加密：

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
