---
title: 教程：将 SQL 按需版本（预览版）连接到 Power BI Desktop 并创建报表
description: 本教程介绍如何在 Azure Synapse Analytics 中将 SQL 按需版本（预览版）连接到 Power BI Desktop 并基于视图创建演示报表。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 649c9a2e0dd9df21a9a59140d9f2999768aab555
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745400"
---
# <a name="tutorial-use-sql-on-demand-preview-with-power-bi-desktop--create-a-report"></a>教程：在 Power BI Desktop 中使用 SQL 按需版本（预览版）并创建报表

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 创建演示数据库
> - 创建用于报表的视图
> - 将 Power BI Desktop 连接到 SQL 按需版本
> - 基于视图创建报表

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要拥有以下软件：

- 一个 SQL 查询工具，例如 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 或 [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)。
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/)。

以下参数的值：

| 参数                                 | 说明                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 按需版本服务终结点地址    | 用作服务器名称                                   |
| SQL 按需版本服务终结点区域     | 用来确定示例中使用的存储 |
| 用来访问终结点的用户名和密码 | 用来访问终结点                               |
| 将用来创建视图的数据库     | 在示例中从其着手的数据库       |

## <a name="1---create-database"></a>1 - 创建数据库

对于演示环境，请创建你自己的演示数据库。 你将使用此数据库来查看元数据，而不是存储实际数据。

通过运行以下 Transact-SQL (T-SQL) 脚本，创建演示数据库（并在必要时删除现有数据库）：

```sql
-- Drop database if it exists
DROP DATABASE IF EXISTS Demo
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-data-source"></a>2 - 创建数据源

SQL 按需版本服务需要使用数据源来访问存储中的文件。 请为终结点所在区域中的存储帐户创建数据源。 尽管 SQL 按需版本可以访问不同区域中的存储帐户，但将存储和终结点置于同一区域可提供更好的性能。

通过运行以下 Transact-SQL (T-SQL) 脚本来创建数据源：

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a secret.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="3---prepare-view"></a>3 - 准备视图

通过运行以下 Transact-SQL (T-SQL) 脚本，基于外部演示数据创建 Power BI 要使用的视图：

利用以下查询在数据库 `Demo` 中创建视图 `usPopulationView`：

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS uspv;
```

演示数据包含以下数据集：

美国每个县按性别和种族划分的人口，数据来源于 2000 年和 2010 年十年一次的人口普查，采用 Parquet 格式。

| 文件夹路径                                                  | 说明                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | 演示存储帐户中的数据的父文件夹               |
| /release/us_population_county/                               | 采用 Parquet 格式的美国人口数据文件，使用 Hive/Hadoop 分区方案按年份分区。 |

## <a name="4---create-power-bi-report"></a>4 - 创建 Power BI 报表

使用以下步骤为 Power BI Desktop 创建报表：

1. 打开 Power BI Desktop 应用程序并选择“获取数据”。

   ![打开 Power BI Desktop 应用程序并选择“获取数据”。](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. 选择“Azure” > “Azure SQL 数据库”。 

   ![选择数据源。](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. 在“服务器”字段中键入数据库所在服务器的名称，然后在数据库名称中键入 `Demo`。 选择“导入”选项，然后选择“确定”。 

   ![选择终结点上的数据库。](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. 选择首选的身份验证方法：

    - AAD 示例 
  
        ![单击“登录”。](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - SQL 登录示例 - 键入你的用户名和密码。

        ![使用 SQL 登录。](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. 选择 `usPopulationView` 视图，然后选择“加载”。 

   ![选择所选数据库上的一个视图。](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. 等待操作完成，然后会显示一个弹出窗口，指出“`There are pending changes in your queries that haven't been applied`”。 选择“应用更改”。 

   ![单击“应用更改”。](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. 等待“应用查询更改”对话框消失，这可能需要几分钟时间。 

   ![等待查询完成。](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. 在加载完成后，按此顺序选择以下列来创建报表：
   - countyName
   - 填充 (population)
   - stateName

   ![选择要关注的列来生成地图报表。](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>清理资源

使用完此报表后，请通过以下步骤删除资源：

1. 删除存储帐户的凭据

   ```sql
   DROP EXTENAL DATA SOURCE AzureOpenData
   ```

2. 删除视图

   ```sql
   DROP VIEW usPopulationView;
   ```

3. 删除数据库

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>后续步骤

转到[查询存储文件](develop-storage-files-overview.md)，了解如何使用 Synapse SQL 来查询存储文件。
