---
title: "连接到 Azure SQL 数据仓库 - SSMS | Microsoft 文档"
description: "使用 SQL Server Management Studio (SSMS) 来连接并查询 Azure SQL 数据仓库。"
services: sql-data-warehouse
documentationcenter: 
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 299e50b3-e68a-471c-8aee-b0b9874781bd
ms.service: sql-data-warehouse
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/31/2016
ms.author: elbutter;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 77474214c6fafe7f591030d30f6a46c66fbc5c09
ms.openlocfilehash: 30ac3558534e96b63d78f9c66d42d11b7c1a3c75


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
要使用本教程，你需要：

* 现有 SQL 数据仓库。 若要创建这样一个数据仓库，请参阅[创建 SQL 数据仓库][创建 SQL 数据仓库]。
* 安装了 SQL Server Management Studio (SSMS)。 如果尚未[安装 SSMS][安装 SSMS]，请免费安装。
* 完全限定的 SQL Server 名称。 若要查找此名称，请参阅[连接到 SQL 数据仓库][连接到 SQL 数据仓库]。

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
4. 若要浏览，请展开你的 Azure SQL 服务器。 你可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2.运行示例查询
现在，你已建立了与数据库的连接，接下来让我们编写查询。

1. 在 SQL Server 对象资源管理器中右键单击你的数据库。
2. 选择“新建查询”。 此时将打开一个新的查询窗口。
   
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
既然你可以执行连接和查询，接下来请尝试[使用 PowerBI 可视化数据][使用 PowerBI 可视化数据]。

若要为 Azure Active Directory 配置环境，请参阅 [SQL 数据仓库身份验证][SQL 数据仓库身份验证]。

<!--Arcticles-->
[连接到 SQL 数据仓库]: sql-data-warehouse-connect-overview.md
[创建 SQL 数据仓库]: sql-data-warehouse-get-started-provision.md
[SQL 数据仓库身份验证]: sql-data-warehouse-authentication.md
[使用 PowerBI 可视化数据]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure 门户]: https://portal.azure.com
[安装 SSMS]: https://msdn.microsoft.com/en-US/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png



<!--HONumber=Nov16_HO3-->


