---
title: 使用 SQL 点播查询 JSON 文件（预览）
description: 本部分介绍如何在 Azure Synapse Analytics 中使用 SQL 点播读取 JSON 文件。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4c1fe9ac5d3b2470fb70231a83e57f3e08d0dfb1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197584"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的 SQL 点播（预览版）查询 JSON 文件

本文介绍如何在 Azure Synapse Analytics 中使用 SQL 点播（预览版）编写查询。 查询的目标是读取 JSON 文件。 [OPENROWSET](develop-openrowset.md)中列出了支持的格式。

## <a name="prerequisites"></a>必备条件

阅读本文其余部分之前，请先查看以下文章：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>示例 JSON 文件

以下部分包含用于读取 JSON 文件的示例脚本。 文件存储在*json*容器和文件夹*书籍*中，并包含具有以下结构的单个图书条目：

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

## <a name="read-json-files"></a>读取 JSON 文件

若要使用 JSON_VALUE 和[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)处理 json 文件，需要将存储中的 json 文件作为单个列来读取。 以下脚本将*book1*文件读取为单个列：

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> 您正在将整个 JSON 文件读取为单个行或列。 因此，FIELDTERMINATOR、FIELDQUOTE 和 ROWTERMINATOR 设置为0x0b。

## <a name="query-json-files-using-json_value"></a>使用 JSON_VALUE 查询 JSON 文件

下面的查询演示了如何使用[JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)从*代中名为概率和统计方法*的书籍中检索标量值（标题、出版商），这是所选文章的简介：

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>使用 JSON_QUERY 查询 JSON 文件

下面的查询演示了如何使用[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)从*代中名为概率和统计方法*的书籍中检索对象和数组（作者），这是所选主题的介绍：

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>使用 OPENJSON 查询 JSON 文件

以下查询使用[OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 它将在名为概率的书籍*和代中的统计方法（所选文章的简介）* 中检索对象和属性：

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>后续步骤

本系列文章中的下一篇文章将演示如何执行以下操作：

- [查询文件夹和多个文件](query-folders-multiple-csv-files.md)
- [创建和使用视图](create-use-views.md)
