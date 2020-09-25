---
title: 使用 SQL On-demand（预览版）查询 Parquet 嵌套类型
description: 在本文中，你将了解如何使用 SQL 点播 (预览) 查询 Parquet 嵌套类型。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 08502704515c791bf63f4803b7446a0471c0a869
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288250"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的 SQL 点播 (预览版) 查询 Parquet 和 JSON 文件中的嵌套类型

本文介绍如何使用 Azure Synapse Analytics 中的 SQL 点播 (预览版) 来编写查询。 查询将读取 Parquet 嵌套类型。
嵌套类型是表示对象或数组的复杂结构。 嵌套类型可以存储在中： 
- [Parquet](query-parquet-files.md)，其中你可以有多个包含数组和对象的复杂列。
- 分层 [JSON 文件](query-json-files.md)，您可以在其中以单个列的形式读取复杂的 json 文档。
- Azure Cosmos DB 集合 (目前处于封闭公共预览) ，其中每个文档都可以包含复杂的嵌套属性。

Azure Synapse SQL 点播将所有嵌套类型的格式设置为 JSON 对象和数组。 因此，您可以 [使用 JSON 函数提取或修改复杂对象](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) ，或 [使用 OPENJSON 函数分析 JSON 数据](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server)。 

下面是一个从 [COVID-19 开放式搜索数据集](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON 文件（其中包含嵌套对象）提取标量和对象值的查询示例： 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`函数从指定路径处的字段返回标量值。 `JSON_QUERY`函数从指定路径处的字段返回格式为 JSON 的对象。

> [!IMPORTANT]
> 此示例使用 COVID-19 开放式研究数据集中的文件。 [请参阅此处的数据的许可和结构](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)。

## <a name="prerequisites"></a>先决条件

第一步是创建将在其中创建数据源的数据库。 然后，您将通过在数据库上运行 [安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) 来初始化这些对象。 安装脚本将创建在示例中使用的数据源、数据库范围的凭据和外部文件格式。

## <a name="project-nested-or-repeated-data"></a>投影嵌套数据或重复数据

Parquet 文件可以包含多个具有复杂类型的列。 这些列中的值设置为 JSON 文本格式，并作为 VARCHAR 列返回。 下面的查询读取 structExample parquet 文件，并演示如何读取嵌套列的值： 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

此查询将返回以下结果。 每个嵌套对象的内容返回为 JSON 文本。

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date"： "2009-04-25"}| {"Time"： "20：51： 54.3598000"}|    {"Timestamp"： "5501-04-08 12：13： 57.4821000"}|    {"Decimal"： 11143412.25350}| {"Float"： 0.5}|
|{"Date"： "1916"}| {"Time"： "00：16： 04.6778000"}|    {"Timestamp"： "1990-06-30 20：50： 52.6828000"}|    {"Decimal"： 1963545.62800}|  {"Float"：-2.125}|

以下查询读取 justSimpleArray.parquet 文件。 它会投影 Parquet 文件中的所有列，包括嵌套的和重复的数据。

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

此查询将返回以下结果：

|SimpleArray|
| --- |
|[11，12，13]|
|[21、22、23]|

## <a name="read-properties-from-nested-object-columns"></a>从嵌套的对象列中读取属性

`JSON_VALUE`函数使您可以从格式化为 JSON 文本的列返回值：

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

结果如下表所示：

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| 补充信息 a epidemiolo .。。 | Julien   | -图 S1： Phylogeny .。。 | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

与 JSON 文件不同，在大多数情况下，它会返回包含复杂 JSON 对象的单列，Parquet 文件可以具有多个复杂列。 您可以通过对每列使用函数来读取嵌套列的属性 `JSON_VALUE` 。 `OPENROWSET` 使您能够直接指定子句中嵌套属性的路径 `WITH` 。 可以将路径设置为列的名称，也可以在列类型后面添加 [JSON 路径表达式](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) 。

下面的查询读取 structExample parquet 文件，并演示如何呈现嵌套列的元素。 可以通过两种方式引用嵌套值：
- 通过在类型规范后指定嵌套值路径表达式。
- 通过使用 do "." 将列名的名称设置为嵌套路径，以引用这些字段。

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>访问重复列中的元素

下面的查询读取 justSimpleArray parquet 文件，并使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 从重复列中检索标量元素，例如数组或映射：

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

结果如下：

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11，12，13] | 11   | 12 | 13 |
| [21、22、23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>从复杂列访问子对象

下面的查询读取 mapExample parquet 文件，并使用 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 从重复列（如数组或映射）中检索非标量元素：

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

还可以在子句中显式引用要返回的列 `WITH` ：

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

结构 `MapOfPersons` 作为 VARCHAR 列返回并格式化为 JSON 字符串。

## <a name="project-values-from-repeated-columns"></a>重复列中的项目值

如果有一组标量值 (例如 `[1,2,3]` ，在某些列中) ，则可以通过使用以下脚本轻松扩展它们并将其与主行联接：

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>后续步骤

下一篇文章将介绍如何[查询 JSON 文件](query-json-files.md)。
