---
title: 使用可视化工作室和 SSDT 连接到并查询 Synapse SQL
description: 使用可视化工作室使用 Azure 同步分析查询 SQL 池。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428558"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>通过可视化工作室和 SSDT 连接到 Synapse SQL
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

使用可视化工作室使用 Azure 同步分析查询 SQL 池。 此方法使用 Visual Studio 2019 中的 SQL Server Data Tools (SSDT) 扩展。 

### <a name="supported-tools-for-sql-on-demand-preview"></a>支持 SQL 按需工具（预览）

当前不支持 SQL 按需（预览）的可视化工作室。 但是，Azure 数据 Studi（预览）o 是一个完全支持的工具。 SQL 服务器管理工作室从版本 18.4 中得到部分支持，并且具有有限的功能，如连接和查询。

## <a name="prerequisites"></a>先决条件
要使用本教程，您需要具有以下组件：

* 现有的 SQL 池。 如果没有 SQL 池，请参阅[创建 SQL 池](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)以完成此先决条件。
* 适用于 Visual Studio 的 SSDT。 如果您有 Visual Studio，则可能已有此组件。 有关安装说明和选项，请参阅 [安装 Visual Studio 和 SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* 完全限定的 SQL Server 名称。 要查找此内容，请参阅[连接到 SQL 池](connect-overview.md)。

## <a name="1-connect-to-sql-pool"></a>1. 连接到 SQL 池
1. 打开 Visual Studio 2019。
2. 打开 SQL 服务器对象资源管理器。 为此，请选择 **"查看** > **SQL 服务器对象资源管理器**"。
   
    ![SQL Server 对象资源管理器](./media/get-started-visual-studio/open-ssdt.png)
3. 单击“添加 SQL Server”**** 图标。
   
    ![添加 SQL 服务器](./media/get-started-visual-studio/add-server.png)
4. 填写“连接到服务器”窗口中的字段。
   
    ![连接到服务器](./media/get-started-visual-studio/connection-dialog.png)
   
   * **服务器名称**：输入以前标识的**服务器名称**。
   * **身份验证**：选择**SQL 服务器身份验证**或**活动目录集成身份验证**：
   * **用户名**和**密码**：如果上面选择了 SQL Server 身份验证，请输入用户名和密码。
   * 单击“连接”。
5. 要浏览，请展开 Azure SQL 服务器。 可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. 运行示例查询
现在，已建立与数据库的连接，您将编写查询。

1. 在 SQL Server 对象资源管理器中右键单击数据库。
2. 选择“新建查询”****。 “新建查询”窗口随即打开。
   
    ![新建查询](./media/get-started-visual-studio/new-query2.png)
3. 将以下 T-SQL 查询复制到查询窗口：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 运行查询。 为此，请单击绿色箭头，或使用以下快捷键： `CTRL`+`SHIFT`+`E`。
   
    ![运行查询](./media/get-started-visual-studio/run-query.png)
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。
   
    ![查询结果](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>后续步骤
现在，您可以连接和查询，请尝试[使用 Power BI 可视化数据](get-started-power-bi-professional.md)。
要为 Azure 活动目录身份验证配置环境，请参阅[对 SQL 池进行身份验证](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
 