---
title: 连接到 Azure SQL 数据仓库 - SSMS | Microsoft 文档
description: 使用 SQL Server Management Studio (SSMS) 来连接并查询 Azure SQL 数据仓库。
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f4b960115da3d30d355768498ae95270acaef49b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 连接到 SQL 数据仓库
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

使用 SQL Server Management Studio (SSMS) 来连接并查询 Azure SQL 数据仓库。 

## <a name="prerequisites"></a>先决条件
要使用本教程，需要：

* 现有 SQL 数据仓库。 若要创建 SQL 数据仓库，请参阅 [创建 SQL 数据仓库][Create a SQL Data Warehouse]。
* 安装了 SQL Server Management Studio (SSMS)。 如果尚未[安装 SSMS][Install SSMS]，可免费安装。
* 完全限定的 SQL Server 名称。 若要查找此名称，请参阅 [连接到 SQL 数据仓库][Connect to SQL Data Warehouse]。

## <a name="1-connect-to-your-sql-data-warehouse"></a>1.连接到 SQL 数据仓库
1. 打开 SSMS。
2. 打开对象资源管理器。 若要执行此操作，选择“**文件**” > “**连接对象资源管理器**”。
   
    ![SQL Server 对象资源管理器][1]
3. 填写“连接到服务器”窗口中的字段。
   
    ![连接到服务器][2]
   
   * **服务器名称**。 输入前面标识的 **服务器名称** 。
   * “身份验证”。 选择“SQL Server 身份验证”或“Active Directory 集成身份验证”。
   * “用户名”和“密码”。 如果上面选择了 SQL Server 身份验证，请输入用户名和密码。
   * 单击“连接”。
4. 要浏览，请展开 Azure SQL 服务器。 可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2.运行示例查询
现在，已建立了与数据库的连接，接下来让我们编写查询。

1. 在 SQL Server 对象资源管理器中右键单击数据库。
2. 选择“新建查询”。 此时会打开一个新的查询窗口。
   
    ![新建查询][4]
3. 将以下 TSQL 查询复制到查询窗口中：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 运行该查询。 为此，请单击 `Execute` 或使用以下快捷键：`F5`。
   
    ![运行查询][5]
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。
   
    ![查询结果][6]

## <a name="next-steps"></a>后续步骤
既然可以执行连接和查询，接下来请尝试 [使用 PowerBI 直观地显示数据][visualizing the data with PowerBI]。

若要为 Azure Active Directory 身份验证配置环境，请参阅[向 SQL 数据仓库进行身份验证][Authenticate to SQL Data Warehouse]。

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
