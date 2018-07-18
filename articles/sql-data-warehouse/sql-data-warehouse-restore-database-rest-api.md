---
title: 还原 Azure SQL 数据仓库 - REST API | Microsoft Docs
description: 使用 REST API 还原 Azure SQL 数据仓库。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e1874fdf7c11d98d369072739c5937caffe6e96
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524418"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>使用 REST API 还原 Azure SQL 数据仓库
使用 REST API 还原 Azure SQL 数据仓库。

## <a name="before-you-begin"></a>开始之前
**验证 DTU 容量。** 每个 SQL 数据仓库都由一个具有默认 [DTU 配额](../sql-database/sql-database-what-is-a-dtu.md)的逻辑 SQL 服务器（例如 myserver.database.windows.net）承载。  在还原 SQL 数据仓库之前，请确保 SQL Server 的剩余 DTU 配额足够进行数据库还原。 若要请求更多 DTU，可以[创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-an-active-or-paused-data-warehouse"></a>还原活动或暂停的数据仓库
若要还原数据仓库，请执行以下操作：

1. 使用“获取数据库还原点”操作获取数据库还原点列表。
2. 使用[创建数据库还原请求](https://msdn.microsoft.com/library/azure/dn509571.aspx)操作开始还原。
3. 使用[数据库操作状态](https://msdn.microsoft.com/library/azure/dn720371.aspx)操作跟踪还原状态。

> [!NOTE]
> 完成还原后，可以按[在恢复后配置数据库](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)中的说明配置恢复后的数据仓库。
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>还原已删除的数据仓库
若要还原已删除的数据仓库，请执行以下操作：

1. 使用[列出可还原的已删除数据库](https://msdn.microsoft.com/library/azure/dn509562.aspx)操作列出所有可还原的已删除数据仓库。
2. 使用[获取可还原的已删除数据库][获取可还原的已删除数据库] 操作获取要还原的已删除数据仓库的详细信息。
3. 使用[创建数据库还原请求](https://msdn.microsoft.com/library/azure/dn509571.aspx)操作开始还原。
4. 使用[数据库操作状态](https://msdn.microsoft.com/library/azure/dn720371.aspx)操作跟踪还原状态。

> [!NOTE]
> 若要在完成还原后配置数据仓库，请参阅[在恢复后配置数据库](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)。
> 
> 

## <a name="next-steps"></a>后续步骤
若要了解 Azure SQL 数据库版本的业务连续性功能，请阅读 [Azure SQL 数据库业务连续性概述](../sql-database/sql-database-business-continuity.md)。
