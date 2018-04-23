---
title: 连接到 Azure SQL 数据仓库 - VSTS | Microsoft 文档
description: 使用 Visual Studio 查询 SQL 数据仓库。
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 1bd3516661a73e7a6d9aaceed589a55cd650453e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="connect-to-sql-data-warehouse-with-visual-studio-and-ssdt"></a>使用 Visual Studio 和 SSDT 连接到 SQL 数据仓库
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

使用 Visual Studio 只需几分钟便可查询 Azure SQL 数据仓库。 此方法使用 Visual Studio 中的 SQL Server Data Tools (SSDT) 扩展。 

## <a name="prerequisites"></a>先决条件
要使用本教程，需要：

* 现有 SQL 数据仓库。 若要创建 SQL 数据仓库，请参阅 [创建 SQL 数据仓库][Create a SQL Data Warehouse]。
* 适用于 Visual Studio 的 SSDT。 如果具有 Visual Studio，则可以已具有此工具。 有关安装说明和选项，请参阅 [安装 Visual Studio 和 SSDT][Installing Visual Studio and SSDT]。
* 完全限定的 SQL Server 名称。 若要查找此名称，请参阅 [连接到 SQL 数据仓库][Connect to SQL Data Warehouse]。

## <a name="1-connect-to-your-sql-data-warehouse"></a>1.连接到 SQL 数据仓库
1. 打开 Visual Studio 2013 或 2015。
2. 打开 SQL Server 对象资源管理器。 为此，请选择“视图” > “SQL Server 对象资源管理器”。
   
    ![SQL Server 对象资源管理器][1]
3. 单击“添加 SQL Server”图标。
   
    ![添加 SQL 服务器][2]
4. 填写“连接到服务器”窗口中的字段。
   
    ![连接到服务器][3]
   
   * **服务器名称**。 输入前面标识的 **服务器名称** 。
   * “身份验证”。 选择“SQL Server 身份验证”或“Active Directory 集成身份验证”。
   * “用户名”和“密码”。 如果上面选择了 SQL Server 身份验证，请输入用户名和密码。
   * 单击“连接”。
5. 要浏览，请展开 Azure SQL 服务器。 可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2.运行示例查询
现在，已建立了与数据库的连接，接下来让我们编写查询。

1. 在 SQL Server 对象资源管理器中右键单击数据库。
2. 选择“新建查询”。 此时会打开一个新的查询窗口。
   
    ![新建查询][5]
3. 将以下 TSQL 查询复制到查询窗口中：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 运行该查询。 为此，请单击绿色箭头，或使用以下快捷键： `CTRL`+`SHIFT`+`E`。
   
    ![运行查询][6]
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。
   
    ![查询结果][7]

## <a name="next-steps"></a>后续步骤
既然可以执行连接和查询，接下来请尝试 [使用 PowerBI 直观地显示数据][visualizing the data with PowerBI]。

若要为 Azure Active Directory 身份验证配置环境，请参阅[向 SQL 数据仓库进行身份验证][Authenticate to SQL Data Warehouse]。

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
