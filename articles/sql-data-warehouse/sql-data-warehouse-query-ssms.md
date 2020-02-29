---
title: 与 SSMS 连接
description: 使用 SQL Server Management Studio （SSMS）连接到 Azure Synapse Analytics 并进行查询。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2109402a874ff8c722bd05e1e5cb62b461cb2292
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198610"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>通过 SQL Server Management Studio （SSMS）连接到 Azure Synapse Analytics
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

使用 SQL Server Management Studio （SSMS）连接到 Azure Synapse 中的数据仓库并对其进行查询。 

## <a name="prerequisites"></a>必备条件
要使用本教程，需要：

* 现有的 SQL 池。 若要创建一个，请参阅[创建 SQL 池](sql-data-warehouse-get-started-provision.md)。
* 安装了 SQL Server Management Studio (SSMS)。 如果尚未[安装 SSMS](https://msdn.microsoft.com/library/hh213248.aspx) ，请免费安装。
* 完全限定的 SQL Server 名称。 若要查找此信息，请参阅[连接到 SQL 池](sql-data-warehouse-connect-overview.md)。

## <a name="1-connect-to-your-sql-pool"></a>1. 连接到 SQL 池
1. 打开 SSMS。
2. 通过选择 "**文件**" > **连接对象资源管理器**打开对象资源管理器。
   
    ![SQL Server 对象资源管理器](media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. 填写“连接到服务器”窗口中的字段。
   
    ![连接到服务器](media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **服务器名称**。 输入前面标识的 **服务器名称** 。
   * “身份验证”。 选择“SQL Server 身份验证”或“Active Directory 集成身份验证”。
   * “用户名”和“密码”。 如果上面选择了 SQL Server 身份验证，请输入用户名和密码。
   * 单击“连接”。
4. 要浏览，请展开 Azure SQL 服务器。 可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW](media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. 运行示例查询
现在，已建立了与数据库的连接，接下来让我们编写查询。

1. 在 SQL Server 对象资源管理器中右键单击数据库。
2. 选择“新建查询”。 “新建查询”窗口随即打开。
   
    ![新建查询]( media/sql-data-warehouse-query-ssms/new-query.png)
3. 将以下 T-sql 查询复制到查询窗口：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 单击 "`Execute`" 运行查询，或使用以下快捷方式： `F5`。
   
    ![运行查询](media/sql-data-warehouse-query-ssms/execute-query.png)
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。
   
    ![查询结果](media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>后续步骤
现在，你可以进行连接和查询，请尝试[通过 Power BI 来可视化数据](sql-data-warehouse-get-started-visualize-with-power-bi.md )。
若要为 Azure Active Directory 身份验证配置环境，请参阅对[SQL 池进行身份验证](sql-data-warehouse-authentication.md)。
