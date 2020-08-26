---
title: 使用 Azure Data Studio（预览版）连接到 Synapse SQL
description: 使用 Azure Data Studio（预览版）连接到 Azure Synapse Analytics 中的 Synapse SQL 并对其进行查询。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 78807db19b413bb642e8dcf61e480d6d954b6178
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059557"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>使用 Azure Data Studio（预览版）连接到 Synapse SQL

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

可以使用 [Azure Data Studio（预览版）](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)连接到 Azure Synapse Analytics 中的 Synapse SQL 并对其进行查询。 

## <a name="connect"></a>连接

若要连接到 Synapse SQL，请打开 Azure Data Studio，然后选择“新建连接”  。

![打开 Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

选择“Microsoft SQL Server”作为“连接类型”   。

连接需要以下参数：

* **服务器：** 服务器的格式为 `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net
* **数据库：** 数据库名称

> [!NOTE]
> 若要使用 SQL 按需版本（预览版）  ，则 URL 应如下所示：
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net。
>
> 若使用 SQL 池，则 URL 应如下所示：
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

选择“Windows 身份验证”、“Azure Active Directory”或“SQL 登录”作为“身份验证类型”。

若要使用“SQL 登录”作为身份验证类型，请添加用户名/密码参数：

* **用户：** 采用 `<User>` 格式的服务器用户
* **密码:** 与用户关联的密码

若要使用 Azure Active Directory，需要选择所需的身份验证类型。

![AAD 身份验证](./media/get-started-azure-data-studio/3-aad-auth.png)

以下屏幕截图显示“Windows 身份验证”  的“连接详细信息”  ：

![Windows 身份验证](./media/get-started-azure-data-studio/3-windows-auth.png)

以下屏幕截图显示使用 SQL 登录名时的“连接详细信息”：

![SQL 登录名](./media/get-started-azure-data-studio/2-database-details.png)

成功登录后，应会看到如下所示的仪表板：![仪表板](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>查询

连接后，可以对实例使用支持的 [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 语句来查询 Synapse SQL。 在仪表板视图中选择“新建查询”  即可开始。

![新建查询](./media/get-started-azure-data-studio/5-new-query.png)

例如，可以使用 SQL 按需版本通过以下 Transact-SQL 语句来[查询 Parquet 文件](query-parquet-files.md)：

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>后续步骤 
了解连接到 Synapse SQL 的其他方式： 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)
 
