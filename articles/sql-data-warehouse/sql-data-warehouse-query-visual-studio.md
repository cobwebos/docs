---
title: "连接到 Azure SQL 数据仓库-VSTS |Microsoft 文档"
description: "使用 Visual Studio 的查询 SQL 数据仓库。"
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: daace889-95e5-4826-b2fc-047eac9d6d95
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 10/31/2016
ms.author: anvang;barbkess
ms.openlocfilehash: 1e44c6c3c47034a892753c69c5ef22a5eac18c0d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="connect-to-sql-data-warehouse-with-visual-studio-and-ssdt"></a>连接到 SQL 数据仓库与 Visual Studio 和 SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

在只需几分钟后，请查询 Azure SQL 数据仓库中使用 Visual Studio。 此方法使用在 Visual Studio 中的 SQL Server Data Tools (SSDT) 扩展。 

## <a name="prerequisites"></a>先决条件
若要使用本教程，你需要：

* 现有的 SQL 数据仓库。 若要创建一个帐户，请参阅[创建 SQL 数据仓库][Create a SQL Data Warehouse]。
* 适用于 Visual Studio 的 SSDT。 如果你有 Visual Studio，你可能已经这。 有关安装说明和选项，请参阅[安装 Visual Studio 和 SSDT][Installing Visual Studio and SSDT]。
* 完全限定的 SQL server 名称。 若要找到此页，请参阅[连接到 SQL 数据仓库][Connect to SQL Data Warehouse]。

## <a name="1-connect-to-your-sql-data-warehouse"></a>1.连接到 SQL 数据仓库
1. 打开 Visual Studio 2013 或 2015年。
2. 打开 SQL Server 对象资源管理器。 若要执行此操作，选择**视图** > **SQL Server 对象资源管理器**。
   
    ![SQL Server 对象资源管理器][1]
3. 单击**添加 SQL Server**图标。
   
    ![添加 SQL Server][2]
4. 填写连接到服务器窗口中的字段。
   
    ![连接到服务器][3]
   
   * **服务器名称**。 输入**服务器名称**先前标识。
   * **身份验证**。 选择**SQL Server 身份验证**或**Active Directory 集成的身份验证**。
   * **用户名称**和**密码**。 如果上面选择了 SQL Server 身份验证，请输入用户名和密码。
   * 单击“连接” 。
5. 若要浏览，请展开你的 Azure SQL server。 你可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2.运行示例查询
现在，已对数据库建立的连接，让我们编写查询。

1. 右键单击 SQL Server 对象资源管理器中的数据库。
2. 选择**新查询**。 打开新查询窗口。
   
    ![新建查询][5]
3. 将以下 TSQL 查询复制到查询窗口中：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 运行查询。 若要执行此操作，单击绿色箭头，或使用以下快捷键： `CTRL` + `SHIFT` + `E`。
   
    ![运行查询][6]
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。
   
    ![查询结果][7]

## <a name="next-steps"></a>后续步骤
现在，你可以连接和查询，请尝试[可视化使用 PowerBI 数据][visualizing the data with PowerBI]。

若要配置 Azure Active Directory 身份验证的环境，请参阅[进行身份验证到 SQL 数据仓库][Authenticate to SQL Data Warehouse]。

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
