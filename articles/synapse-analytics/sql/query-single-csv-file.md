---
title: 使用 SQL 按需版本（预览版）查询 CSV 文件
description: 本文介绍如何使用 SQL 按需版本（预览版）查询不同文件格式的单个 CSV 文件。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 628631fb7fddbc07dcb865e3d3badbfb608ad097
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214445"
---
# <a name="query-csv-files"></a>查询 CSV 文件

在本文中，你将了解如何在 Azure Synapse Analytics 中使用 SQL 按需版本（预览版）查询单个 CSV 文件。 CSV 文件可有多种不同的格式： 

- 带有或不带标题行
- 逗号和制表符分隔的值
- Windows 和 Unix 样式行尾
- 不带引号和带引号的值，以及转义字符

上述所有类型都将在下文中进行介绍。

## <a name="prerequisites"></a>先决条件

第一步是创建将在其中创建表的数据库。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据以及在这些示例中使用的外部文件格式。

## <a name="windows-style-new-line"></a>Windows 样式换行符

以下查询展示了如何读取不包含标题行、包含 Windows 样式换行符和逗号分隔列的 CSV 文件。

文件预览：

![不带标题的 CSV 文件的前 10 行，Windows 样式的换行符。](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="unix-style-new-line"></a>Unix 样式换行符

以下查询展示了如何读取不包含标题行、包含 Unix 样式换行符和逗号分隔列的文件。 请注意文件位置，相较其他示例中有何不同。

文件预览：

![不带标题行、带 Unix 样式换行符的 CSV 文件的前 10 行。](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

以下查询展示了如何读取带标题行、带 Unix 样式换行符和逗号分隔列的文件。 请注意文件位置，相较其他示例中有何不同。

文件预览：

![带标题行、带 Unix 样式换行符的 CSV 文件的前 10 行。](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="custom-quote-character"></a>自定义引证字符

以下查询展示了如何读取包含标题行、包含 Unix 样式换行符、逗号分隔列和引证值的文件。 请注意文件位置，相较其他示例中有何不同。

文件预览：

![带标题行、带 Unix 样式换行符和引证值的 CSV 文件的前 10 行。](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> 如果省略 FIELDQUOTE 参数，此查询会返回相同的结果，因为 FIELDQUOTE 的默认值是双引号。

## <a name="escaping-characters"></a>转义字符

以下查询展示了如何读取包含标题行、包含 Unix 样式换行符、逗号分隔列和用于值内字段分隔符（逗号）的转义字符的文件。 请注意文件位置，相较其他示例中有何不同。

文件预览：

![CSV 文件的前 10 行，其中包含了标题行、带 Unix 样式换行符和用于字段分隔符的转义字符。](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
    country_name = 'Slovenia';
```

> [!NOTE]
> 如果未指定 ESCAPECHAR，此查询将失败，因为 "Slov,enia" 中的逗号将被视为字段分隔符，而不是国家/地区名称的一部分。 "Slov,enia" 将被视为两个列。 因此，该特定行将比其他行多一列，并且比 WITH 子句中定义的列数多一列。

### <a name="escaping-quoting-characters"></a>转义引用字符

下面的查询显示了如何读取带有标题行的文件，其中包含 Unix 样式的新行、逗号分隔的列和值中的转义双引号字符。 请注意文件位置，相较其他示例中有何不同。

文件预览：

![下面的查询显示了如何读取带有标题行的文件，其中包含 Unix 样式的新行、逗号分隔的列和值中的转义双引号字符。](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
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
    country_name = 'Slovenia';
```

> [!NOTE]
> 必须使用其他引号字符来转义引号字符。 要让引号字符出现在列值内，必须将值放在引号中。

## <a name="tab-delimited-files"></a>制表符分隔的文件

以下查询展示了如何读取包含标题行、包含 Unix 样式换行符和制表符分隔列的文件。 请注意文件位置，相较其他示例中有何不同。

文件预览：

![带标题行、带 Unix 样式换行符和制表符的 CSV 文件的前 10 行。](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

到目前为止，已通过使用 WITH 和列出所有列来指定了 CSV 文件架构。 可以通过对所需的每个列使用序号来仅指定查询中实际要用的列。 这样将忽略不需要使用的列。

下面的查询返回文件中不同国家/地区名称的数量，并且仅指定所需的列：

> [!NOTE]
> 在下面的查询中查看 WITH 子句，并注意，定义了 [country_name] 列的行的末尾处带有“2”（不带引号）。 这意味着 [country_name] 列是文件中的第二列。 查询将忽略文件中除第二个列以外的所有列。

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>后续步骤

后续文章将介绍如何：

- [查询 Parquet 文件](query-parquet-files.md)
- [查询文件夹和多个文件](query-folders-multiple-csv-files.md)
