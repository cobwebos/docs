---
title: 使用 SQL 按需查询 CSV 文件（预览）
description: 在本文中，您将学习如何使用 SQL 按需（预览）查询具有不同文件格式的单个 CSV 文件。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431587"
---
# <a name="query-csv-files"></a>查询 CSV 文件

在本文中，您将学习如何在 Azure 同步分析中使用 SQL 按需（预览）查询单个 CSV 文件。 CSV 文件可能具有不同的格式： 

- 带和无标题行
- 逗号和制表符分隔值
- 窗口和 Unix 样式行结尾
- 非引号和引号值以及转义字符

下面将介绍上述所有变体。

## <a name="prerequisites"></a>先决条件

在阅读本文的其余部分之前，请查看以下文章：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Windows 样式新行

以下查询演示如何读取没有标题行、Windows 样式的新行和逗号分隔列的 CSV 文件。

文件预览：

![CSV 文件的前 10 行没有标头，Windows 样式新行。](./media/query-single-csv-file/population.png)

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

## <a name="unix-style-new-line"></a>Unix 风格的新行

以下查询演示如何读取没有标题行、具有 Unix 样式的新行和逗号分隔列的文件。 请注意文件与其他示例相比的不同位置。

文件预览：

![CSV 文件的前 10 行没有头行和 Unix 样式新行。](./media/query-single-csv-file/population-unix.png)

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

以下查询演示如何使用头行、Unix 样式的新行和逗号分隔列的读取文件。 请注意文件与其他示例相比的不同位置。

文件预览：

![CSV 文件的前 10 行，包含头行和 Unix 样式新行。](./media/query-single-csv-file/population-unix-hdr.png)

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

## <a name="custom-quote-character"></a>自定义报价字符

以下查询演示如何读取具有头行、具有 Unix 样式的新行、逗号分隔列和引号值的文件。 请注意文件与其他示例相比的不同位置。

文件预览：

![CSV 文件的前 10 行，包含头行和 Unix 样式新行和引用值。](./media/query-single-csv-file/population-unix-hdr-quoted.png)

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
> 如果省略 FIELDQUOTE 参数，则此查询将返回相同的结果，因为 FIELDQUOTE 的默认值为双引号。

## <a name="escaping-characters"></a>正在转义字符

以下查询演示如何读取具有头行的文件，该文件具有 Unix 样式的新行、逗号分隔列和用于值内字段分隔符 （逗号）的转义字符。 请注意文件与其他示例相比的不同位置。

文件预览：

![CSV 文件的前 10 行，包含头行和 Unix 样式新行和用于字段分隔符的转义字符。](./media/query-single-csv-file/population-unix-hdr-escape.png)

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
> 如果未指定 ESCAPECHAR，则此查询将失败，因为"Slov，enia"中的逗号将被视为字段分隔符，而不是国家名称的一部分。 "斯洛夫，伊尼亚"将被视为两列。 因此，特定行的列数将大于其他行，并且一列的列将超过在 WITH 子句中定义的列。

## <a name="tab-delimited-files"></a>选项卡分隔文件

以下查询演示如何使用头行、Unix 样式的新行和选项卡分隔列读取文件。 请注意文件与其他示例相比的不同位置。

文件预览：

![CSV 文件的前 10 行，包含头行和 Unix 样式新行和选项卡分隔符。](./media/query-single-csv-file/population-unix-hdr-tsv.png)

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

## <a name="returning-subset-of-columns"></a>返回列子集

到目前为止，您已经使用 WITH 并列出所有列指定了 CSV 文件架构。 您只能为每个所需的列使用一个批号来指定查询中实际需要的列。 您还将省略不感兴趣的列。

以下查询返回文件中不同的国家/地区名称数，仅指定所需的列：

> [!NOTE]
> 请查看下面的查询中的 WITH 子句，并注意行末尾有"2"（无引号），您可以在其中定义 *[country_name]* 列。 这意味着 *[country_name]* 列是文件中的第二列。 查询将忽略文件中的所有列，但第二列除外。

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

下一篇文章将向您展示如何：

- [查询 Parquet 文件](query-parquet-files.md)
- [查询文件夹和多个文件](query-folders-multiple-csv-files.md)
