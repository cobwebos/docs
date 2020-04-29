---
title: 使用 SQL 点播查询 CSV 文件（预览）
description: 本文介绍如何使用 SQL 点播（预览版）以不同的文件格式查询单个 CSV 文件。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431587"
---
# <a name="query-csv-files"></a>查询 CSV 文件

本文介绍如何在 Azure Synapse Analytics 中使用 SQL 点播（预览版）查询单个 CSV 文件。 CSV 文件的格式可能不同： 

- 带有或不带标题行的
- 逗号和制表符分隔的值
- Windows 和 Unix 样式行尾
- 非引号和带引号的值以及转义字符

所有上述变化都将在下面介绍。

## <a name="prerequisites"></a>必备条件

阅读本文其余部分之前，请先查看以下文章：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Windows 样式新行

下面的查询演示了如何使用 Windows 样式的新行和逗号分隔的列读取没有标题行的 CSV 文件。

文件预览：

![不带标题的 CSV 文件的前 10 行，Windows 样式的换行符。](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Unix 样式的新行

下面的查询演示了如何使用 Unix 样式的新行和逗号分隔的列来读取没有标题行的文件。 与其他示例相比，请注意文件的不同位置。

文件预览：

![不带标头的 CSV 文件和 Unix 样式的新行的前10行。](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>标题行

下面的查询演示了如何使用带有标题行的读取文件、Unix 样式的新行和逗号分隔的列。 与其他示例相比，请注意文件的不同位置。

文件预览：

![带有标题行和 Unix 样式的新行的 CSV 文件前10行。](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>自定义引号字符

下面的查询显示了如何读取带有标题行的文件，以及 Unix 样式的新行、逗号分隔的列和带引号的值。 与其他示例相比，请注意文件的不同位置。

文件预览：

![带有标题行并带有 Unix 样式的新行和带引号的值的 CSV 文件的前10行。](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> 如果省略 FIELDQUOTE 参数，此查询将返回相同的结果，因为 FIELDQUOTE 的默认值为双引号。

## <a name="escaping-characters"></a>转义字符

下面的查询显示了如何读取带有标题行的文件、具有 Unix 样式的新行、逗号分隔的列以及用于字段分隔符（逗号）的转义字符。 与其他示例相比，请注意文件的不同位置。

文件预览：

![带有标题行并带有 Unix 样式的新行和转义字符（用于字段分隔符）的 CSV 文件的前10行。](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slov,enia';
```

> [!NOTE]
> 如果未指定 ESCAPECHAR，此查询将失败，因为 "Slov，enia" 中的逗号将被视为字段分隔符，而不是国家/地区名称的一部分。 "Slov，enia" 将被视为两个列。 因此，特定行将有一列以上的行，而不是在 WITH 子句中定义的列。

## <a name="tab-delimited-files"></a>制表符分隔的文件

下面的查询显示了如何读取带有标题行的文件、Unix 样式的新行和制表符分隔的列。 与其他示例相比，请注意文件的不同位置。

文件预览：

![带有标题行和 Unix 样式的新行和制表符分隔符的 CSV 文件的前10行。](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>返回列的子集

到目前为止，您已使用 WITH 和列出所有列指定了 CSV 文件架构。 仅可通过对所需的每个列使用序号来指定查询中实际需要的列。 你还将忽略不感兴趣的列。

下面的查询返回文件中不同国家/地区名称的数目，仅指定所需的列：

> [!NOTE]
> 在下面的查询中查看 WITH 子句，并注意在您定义 *[country_name]* 列的行尾有 "2" （不带引号）。 这意味着 *[country_name]* 列是文件中的第二列。 查询将忽略文件中的所有列（第二个列除外）。

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>后续步骤

后续文章将介绍如何：

- [查询 Parquet 文件](query-parquet-files.md)
- [查询文件夹和多个文件](query-folders-multiple-csv-files.md)
