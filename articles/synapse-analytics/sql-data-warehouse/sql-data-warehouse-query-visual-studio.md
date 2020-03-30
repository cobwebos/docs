---
title: 与 VSTS 连接
description: 使用可视化工作室查询 Azure 同步分析。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e986515a911cf1bbd88dfc73c56efcc6e81826d6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351404"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>通过可视化工作室和 SSDT 连接到 Azure 同步分析
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

使用 Visual Studio 只需几分钟即可查询 Azure 突触中的 SQL 池。 此方法使用 Visual Studio 2019 中的 SQL Server Data Tools (SSDT) 扩展。 

## <a name="prerequisites"></a>先决条件
要使用本教程，需要：

* 现有的 SQL 池。 要创建一个，请参阅[创建 SQL 池](create-data-warehouse-portal.md)。
* 适用于 Visual Studio 的 SSDT。 如果您有视觉工作室，您可能已经拥有用于视觉工作室的 SSDT。 有关安装说明和选项，请参阅 [安装 Visual Studio 和 SSDT](sql-data-warehouse-install-visual-studio.md)。
* 完全限定的 SQL Server 名称。 要查找此信息，请参阅[连接到 SQL 池](sql-data-warehouse-connect-overview.md)。

## <a name="1-connect-to-your-sql-pool"></a>1. 连接到 SQL 池
1. 打开 Visual Studio 2019。
2. 通过选择**视图** > **SQL Server 对象资源管理器打开 SQL Server 对象资源管理器**。
   
    ![SQL Server 对象资源管理器](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. 单击“添加 SQL Server”**** 图标。
   
    ![添加 SQL 服务器](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. 填写“连接到服务器”窗口中的字段。
   
    ![连接到服务器](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **服务器名称**. 输入前面标识的 **服务器名称** 。
   * **身份验证**。 选择“SQL Server 身份验证”**** 或“Active Directory 集成身份验证”****。
   * **用户名**和**密码**。 如果上面选择了 SQL Server 身份验证，请输入用户名和密码。
   * 单击“连接”。
5. 要浏览，请展开 Azure SQL 服务器。 可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. 运行示例查询
现在，已建立了与数据库的连接，接下来让我们编写查询。

1. 在 SQL Server 对象资源管理器中右键单击数据库。
2. 选择“新建查询”****。 “新建查询”窗口随即打开。
   
    ![新建查询](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. 将以下 T-SQL 查询复制到查询窗口：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 单击绿色箭头或使用以下快捷方式运行查询： `CTRL` + `SHIFT` + `E`。
   
    ![运行查询](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。
   
    ![查询结果](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>后续步骤
现在，您可以连接和查询，请尝试[使用 Power BI 可视化数据](sql-data-warehouse-get-started-visualize-with-power-bi.md)。

要为 Azure 活动目录身份验证配置环境，请参阅[对 SQL 池进行身份验证](sql-data-warehouse-authentication.md)。