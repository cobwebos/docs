---
title: 使用 Visual Studio 和 SSDT 连接到 Synapse SQL 并进行查询
description: 通过 Visual Studio 使用 Azure Synapse Analytics 查询 SQL 池。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a8839609856bda5304712405ec57accb4afb095
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201679"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>使用 Visual Studio 和 SSDT 连接到 Synapse SQL
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

通过 Visual Studio 使用 Azure Synapse Analytics 查询 SQL 池。 此方法使用 Visual Studio 2019 中的 SQL Server Data Tools (SSDT) 扩展。 

> [!NOTE]
> SSDT 不支持 SQL 按需版本（预览版）。

## <a name="prerequisites"></a>先决条件
若要使用本教程，需要具备以下组件：

* 现有的 SQL 池。 如果没有，请参阅[创建 SQL 池](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)以完成此先决条件要求的操作。
* 适用于 Visual Studio 的 SSDT。 如果安装了 Visual Studio，则可能已有了此组件。 有关安装指说明和选项，请参阅 [安装 Visual Studio 和 SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* 完全限定的 SQL Server 名称。 若要查找此名称，请参阅[连接到 SQL 池](connect-overview.md)。

## <a name="1-connect-to-sql-pool"></a>1.连接到 SQL 池
1. 打开 Visual Studio 2019。
2. 打开 SQL Server 对象资源管理器。 为此，请选择“视图” > “SQL Server 对象资源管理器”。
   
    ![SQL Server 对象资源管理器](./media/get-started-visual-studio/open-ssdt.png)
3. 单击“添加 SQL Server”图标。
   
    ![添加 SQL 服务器](./media/get-started-visual-studio/add-server.png)
4. 填写“连接到服务器”窗口中的字段。
   
    ![连接到服务器](./media/get-started-visual-studio/connection-dialog.png)
   
   * **服务器名称**：输入前面标识的 **服务器名称** 。
   * **身份验证**：选择“SQL Server 身份验证”或“Active Directory 集成身份验证”：
   * **用户名**和**密码**：如果在上面选择了“SQL Server 身份验证”，请输入用户名和密码。
   * 单击“连接”。
5. 要浏览，请展开 Azure SQL 服务器。 可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2.运行示例查询
现在已建立了与数据库的连接，你将编写一个查询。

1. 在 SQL Server 对象资源管理器中右键单击数据库。
2. 选择“新建查询”。 “新建查询”窗口随即打开。
   
    ![新建查询](./media/get-started-visual-studio/new-query2.png)
3. 将以下 T-SQL 查询复制到查询窗口中：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 运行查询。 为此，请单击绿色箭头，或使用以下快捷键： `CTRL`+`SHIFT`+`E`。
   
    ![运行查询](./media/get-started-visual-studio/run-query.png)
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。
   
    ![查询结果](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>后续步骤
可以进行连接和查询后，接下来请尝试[使用 Power BI 可视化数据](get-started-power-bi-professional.md)。
若要配置适用于 Azure Active Directory 身份验证的环境，请参阅[向 SQL 池进行身份验证](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
 