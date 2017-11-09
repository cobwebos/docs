---
title: "管理 Azure SQL 数据库组 | Microsoft Docs"
description: "演练如何创建和管理弹性作业。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: f858344d-085b-4022-935e-1b5fa20adbac
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 9ccd7d78169fa5324808e91724e8e193b56b0290
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>使用弹性作业创建和管理扩大的 Azure SQL 数据库（预览）


**弹性数据库作业**可以通过执行架构更改、凭据管理、引用数据更新、性能数据收集或租户（客户）遥测收集等管理操作，简化多组数据库的管理。 目前可以通过 Azure 门户和 PowerShell cmdlet 使用弹性数据库作业。 但是，它在 Azure 门户中的功能被缩减，仅限针对[弹性池（预览）](sql-database-elastic-pool.md)中的所有数据库执行操作。 若要访问其他功能并针对包含自定义集合或分片集（使用[弹性数据库客户端库](sql-database-elastic-scale-introduction.md)创建）的一组数据库执行脚本，请参阅[使用 PowerShell 创建和管理作业](sql-database-elastic-jobs-powershell.md)。 有关作业的详细信息，请参阅[弹性数据库作业概述](sql-database-elastic-jobs-overview.md)。 

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 如需免费试用，请参阅[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 弹性池。 请参阅[关于弹性池](sql-database-elastic-pool.md)的信息。
* 安装弹性数据库作业服务组件。 请参阅[安装弹性数据库作业服务](sql-database-elastic-jobs-service-installation.md)。

## <a name="creating-jobs"></a>创建作业
1. 使用 [Azure 门户](https://portal.azure.com)，从现有的弹性数据库作业池中单击“**创建作业**”。
2. 键入作业控制数据库（作业的元数据存储）的数据库管理员（在安装作业时创建）的用户名和密码。
   
    ![为作业命名、键入或粘贴代码，并单击“运行”][1]
3. 在“**创建作业**”边栏选项卡中，键入作业的名称。
4. 键入具有足够权限、用于连接到目标数据库的用户名和密码，使脚本执行成功。
5. 粘贴或键入 T-SQL 脚本。
6. 单击“**保存**”，并单击“**运行**”。
   
    ![创建并运行作业][5]

## <a name="run-idempotent-jobs"></a>运行幂等作业
针对一组数据库运行某个脚本时，必须确保该脚本是幂等的。 也就是说，该脚本必须能够运行多次，即使它在进入未完成状态之前已失败。 例如，当脚本失败时，作业会自动重试直到成功（在限制次数内，因为重试逻辑最终会停止重试）。 执行此操作的方法是使用一个“IF EXISTS”子句，并在新建对象之前删除找到的任何实例。 下面显示了一个示例：

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

或者，在新建实例之前使用“IF NOT EXISTS”子句：

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

然后，此脚本将更新以前创建的表。

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>检查作业状态
作业开始后，可以检查其进度。

1. 在弹性池页中，单击“管理作业”。
   
    ![单击“管理作业”][2]
2. 单击作业的名称 (a)。 “**状态**”可能是“已完成”或“失败”。 将显示作业的详细信息 (b) 及其创建和运行日期与时间。 以下列表 (c) 显示了针对池中每个数据库运行的脚本的进度，同时还提供了该脚本的日期和时间详细信息。
   
    ![检查已完成的作业][3]

## <a name="checking-failed-jobs"></a>检查失败的作业
如果作业失败，可以查找该作业的执行日志。 单击失败作业的名称即可查看其详细信息。

![检查失败的作业][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


