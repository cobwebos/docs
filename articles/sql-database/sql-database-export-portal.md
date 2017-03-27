---
title: "Azure 门户：将 Azure SQL 数据库导出到 BACPAC 文件 | Microsoft Docs"
description: "使用 Azure 门户将 Azure SQL 数据库导出到 BACPAC 文件"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>使用 Azure 门户将 Azure SQL 数据库导出到 BACPAC 文件

本文介绍了如何使用 [Azure 门户](https://portal.azure.com)将 Azure SQL 数据库导出到 BACPAC 文件（存于 Azure blob 存储中）。 有关导出到 BACPAC 文件的概述，请参阅[导出到 BACPAC](sql-database-export.md)。

> [!NOTE]
> 还可使用 [SQL Server Management Studio](sql-database-export-ssms.md)、[PowerShell](sql-database-export-powershell.md) 或 [SQLPackage](sql-database-export-sqlpackage.md) 将 Azure SQL 数据库文件导出到 BACPAC 文件。
>

## <a name="prerequisites"></a>先决条件

若要完成本文，需要以下各项：

* Azure 订阅。
* Azure SQL 数据库。 
* [Azure 标准存储帐户](../storage/storage-create-storage-account.md)，具有可在标准存储中存储 BACPAC 的 blob 容器。

## <a name="export-your-database"></a>导出数据库

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 打开要导出的数据库对应的 SQL 数据库边栏选项卡。
3. 确保导出期间不发生任何事务。 

   > [!IMPORTANT]
   > 若要保证 BACPAC 文件在事务方面保持一致，请[创建数据库副本](sql-database-copy.md)并从该副本中导出。 
   > 

4. 单击“SQL 数据库”。
5. 单击要存档的数据库。
6. 在 SQL 数据库边栏选项卡中，单击“导出”以打开“导出数据库”边栏选项卡：
   
   ![导出按钮][1]
7. 单击“存储”并选择存储帐户和 blob 容器以存储 BACPAC：
   
   ![导出数据库][2]
8. 选择身份验证类型。 
9. 为包含要导出的数据库的 Azure SQL Server 输入相应的身份验证凭据。
10. 单击“确定”导出数据库。 单击“确定”会创建一个导出数据库请求并将其提交给服务。 导出所需要的时间取决于数据库的大小和复杂程度，以及你的服务级别。 
11. 查看收到的通知。
   
   ![导出通知][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>监视导出操作的进度
1. 单击“SQL Server”。
2. 单击包含你存档的原始（源）数据库的服务器。
3. 向下滚动到操作。
4. 在 SQL 服务器边栏选项卡中，单击“导入/导出历史记录”：
   
   ![导入导出历史记录][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>确认 BACPAC 位于你的存储容器中
1. 单击“存储帐户”。
2. 单击你在其中存储 BACPAC 存档的存储帐户。
3. 单击“容器”，然后选择数据库所导出到的容器以了解详细信息（从这里可以下载和保存 BACPAC）。
   
   ![.bacpac 文件详细信息][5]    

## <a name="next-steps"></a>后续步骤

* 若要了解 Azure SQL 数据库备份的长期备份保留 - 作为导出数据库进行存档的替代方法，请参阅[长期备份保留](sql-database-long-term-retention.md)
* 若要了解如何将 BACPAC 导入到 SQL 数据库，请参阅[将 BACPCAC 导入 SQL 数据库](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


