---
title: 快速入门：使用 SQL 按需版本
description: 本快速入门介绍如何使用 SQL 按需版本（预览版）轻松查询各种类型的文件。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fe07192b0077518cdd73092f53342c298034cfa8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "86274163"
---
# <a name="quickstart-use-sql-on-demand"></a>快速入门：使用 SQL 按需版本

Synapse SQL 按需版本（预览版）是一个无服务器查询服务，可用于针对 Azure 存储中放置的文件运行 SQL 查询。 本快速入门将介绍如何使用 SQL 按需版本查询各种类型的文件。 [OPENROWSET](sql/develop-openrowset.md) 中列出了支持的格式。

本快速入门展示如何查询：CSV、Apache Parquet 和 JSON 文件。

## <a name="prerequisites"></a>先决条件

选择用于发出查询的 SQL 客户端：

- [Azure Synapse Studio](quickstart-synapse-studio.md) 是一个 Web 工具，可用于浏览存储中的文件以及创建 SQL 查询。
- [Azure Data Studio](sql/get-started-azure-data-studio.md) 是一个客户端工具，可用于针对按需数据库运行 SQL 查询和笔记本。
- [SQL Server Management Studio](sql/get-started-ssms.md) 是一个客户端工具，可用于针对按需数据库运行 SQL 查询。

本快速入门的参数：

| 参数                                 | 说明                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 按需版本服务终结点地址    | 用作服务器名称                                   |
| SQL 按需版本服务终结点区域     | 用于确定要在示例中使用的存储 |
| 用于访问终结点的用户名和密码 | 用于访问终结点                               |
| 用于创建视图的数据库         | 在示例中从其着手的数据库       |

## <a name="first-time-setup"></a>首次设置

在使用示例之前，请执行以下操作：

- 为视图创建数据库（若要使用视图）
- 创建供 SQL 按需版本用来访问存储中的文件的凭据

### <a name="create-database"></a>创建数据库

创建自己的用于演示目的的数据库。 你将使用此数据库来创建视图，并将此数据库用于本文中的示例查询。

> [!NOTE]
> 数据库仅用于视图元数据，而不用于实际数据。
>请记下数据库名称，以便稍后在本快速入门中使用。

使用以下查询（请将 `mydbname` 更改为所选名称）：

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>创建数据源

若要使用 SQL 按需版本运行查询，请创建供 SQL 按需版本用来访问存储中的文件的数据源。
执行以下代码片段，以创建此部分的示例中使用的数据源：

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>查询 CSV 文件

下图是要查询的文件的预览：

![不带标题的 CSV 文件的前 10 行，Windows 样式的换行符。](./sql/media/query-single-csv-file/population.png)

以下查询展示了如何读取不包含标题行、包含 Windows 样式换行符和逗号分隔列的 CSV 文件：

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

可以在查询编译时指定架构。
如需更多示例，请参阅[如何查询 CSV 文件](sql/query-single-csv-file.md)。

## <a name="query-parquet-files"></a>查询 Parquet 文件

以下示例演示用于查询 Parquet 文件的自动架构推理功能。 该示例在不指定架构的情况下返回 2017 年 9 月的行数。

> [!NOTE]
> 读取 Parquet 文件时，不一定要在 `OPENROWSET WITH` 子句中指定列。 在这种情况下，SQL 按需版本会利用 Parquet 文件中的元数据，并按名称绑定列。

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

详细了解如何[查询 Parquet 文件](sql/query-parquet-files.md)。

## <a name="query-json-files"></a>查询 JSON 文件

### <a name="json-sample-file"></a>JSON 示例文件

文件存储在 json 容器的文件夹 books 中，包含采用以下结构的单个书籍条目： 

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="query-json-files"></a>查询 JSON 文件

以下查询展示了如何使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 在标题为“Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles”的书籍中检索标量值（标题、出版商）：

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> 我们将整个 JSON 文件读取为单个行/列。 因此 FIELDTERMINATOR、FIELDQUOTE 和 ROWTERMINATOR 已设置为 0x0b，原因是我们预期不会在文件中找到它。

## <a name="next-steps"></a>后续步骤

现在，你可以继续学习以下文章了：

- [查询单个 CSV 文件](sql/query-single-csv-file.md)
- [查询文件夹和多个 CSV 文件](sql/query-folders-multiple-csv-files.md)
- [查询特定的文件](sql/query-specific-files.md)
- [查询 Parquet 文件](sql/query-parquet-files.md)
- [查询 Parquet 嵌套类型](sql/query-parquet-nested-types.md)
- [查询 JSON 文件](sql/query-json-files.md)
- [创建和使用视图](sql/create-use-views.md)
- [创建和使用外部表](sql/create-use-external-tables.md)
- [将查询结果保存到 Azure 存储](sql/create-external-table-as-select.md)
- [查询单个 CSV 文件](sql/query-single-csv-file.md)
