---
title: "使用 Azure 门户复制 Azure SQL 数据库 | Microsoft 文档"
description: "创建 Azure SQL 数据库的副本"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 752dcb97101f4a8288125e3e41167095620d130f
ms.lasthandoff: 03/28/2017


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>使用 Azure 门户复制 Azure SQL 数据库

以下步骤说明如何使用 [Azure 门户](https://portal.azure.com)将 SQL 数据库复制到同一服务器或其他服务器。 

> [!NOTE]
> 还可使用 [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) 或 [Transact-SQL](sql-database-copy-transact-sql.md) 复制 SQL 数据库。
>

若要复制 SQL 数据库，需要做好以下项：

* Azure 订阅。 如果你需要 Azure 订阅，只需单击本页顶部的“免费试用”，然后再回来完成本文的相关操作即可。
* 要复制的 SQL 数据库。 如果你没有 SQL 数据库，请按照[创建你的第一个 Azure SQL 数据库](sql-database-get-started.md)文章中的步骤创建一个。

## <a name="copy-your-sql-database"></a>复制 SQL 数据库
打开要复制的数据库对应的 SQL 数据库页：

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 单击“更多服务” > “SQL 数据库”，然后单击所需的数据库。
3. 在 SQL 数据库页上，单击“复制”：
   
   ![SQL 数据库](./media/sql-database-copy-portal/sql-database-copy.png)
4. 在“复制”页上，提供了一个默认的数据库名称。 根据需要键入不同的名称（服务器上的所有数据库必须具有唯一的名称）。
5. 选择**目标服务器**。 目标服务器是要在其中创建数据库副本的位置。 可以将数据库复制到相同或不同的服务器。 可以创建新的服务器，或者从列表中选择现有的服务器。 
6. 选择“目标服务器”后，将启用“弹性池”和“定价层”选项。 如果服务器包含一个池，可以将数据库复制到该池中。
7. 单击“确定”开始复制过程。
   
   ![SQL 数据库](./media/sql-database-copy-portal/copy-page.png)

## <a name="monitor-the-progress-of-the-copy-operation"></a>监视复制操作的进度
* 开始复制后，单击门户通知了解详细信息。
  
    ![通知][3]
  
    ![监视][4]

## <a name="verify-the-database-is-live-on-the-server"></a>验证数据库位于服务器上
* 单击“更多服务” > “SQL 数据库”并检查新数据库是否处于“联机”状态。

## <a name="resolve-logins"></a>解析登录名
若要在复制操作完成后解析登录名，请参阅[解析登录名](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="next-steps"></a>后续步骤
* 若要了解如何在将数据库复制到其他逻辑服务器时管理用户和登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库的安全性](sql-database-geo-replication-security-config.md)。
* 若要使用 Azure 门户将数据库导出到 BACPAC 文件，请参阅[使用 Azure 门户将数据库导出到 BACPAC](sql-database-export-portal.md)。
* [业务连续性概述](sql-database-business-continuity.md)
* [SQL 数据库文档](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->

[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png


