---
title: 概述 - 使用 SQL 按需版本（预览版）查询存储中的数据
description: 本文描述了如何使用 SQL 按需版本（预览版）资源在 Azure Synapse Analytics 中查询 Azure 存储。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4f78928c26b595caafd6709a200297d62ce1c361
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259671"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>使用 SQL 按需版本（预览版）资源在 Synapse SQL 中查询存储文件

使用 SQL 按需版本（预览版）可以查询数据湖中的数据。 SQL 按需版本提供一个可以适应半结构化和非结构化数据查询的 T-SQL 查询外围应用。 对于查询，T-SQL 的以下方面受支持：

- 完整的 [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 外围应用，包括大部分 [SQL 函数和运算符](overview-features.md)。
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) 会创建一个[外部表](develop-tables-external-tables.md)，然后将 Transact-SQL SELECT 语句的结果并行导出到 Azure 存储。

有关当前支持和不支持的功能的详细信息，请参阅 [SQL 按需版本概述](on-demand-workspace-overview.md)一文或以下文章：
- [开发存储访问](develop-storage-files-overview.md)，你可以在其中了解如何使用[外部表](develop-tables-external-tables.md)和 [OPENROWSET](develop-openrowset.md) 函数从存储中读取数据。
- [控制存储访问](develop-storage-files-storage-access-control.md)，你可以在其中了解如何使用 SAS 身份验证或工作区的托管标识启用 Synapse SQL 以访问存储。

## <a name="overview"></a>概述

为了支持在就地查询 Azure 存储文件中的数据方面提供顺畅的体验，SQL 按需版本将使用具有以下附加功能的 [OPENROWSET](develop-openrowset.md) 函数：

- [查询多个文件或文件夹](#query-multiple-files-or-folders)
- [PARQUET 文件格式](#query-parquet-files)
- [查询 CSV 和分隔文本（字段终止符、行终止符、转义符）](#query-csv-files)
- [读取选定的列子集](#read-a-chosen-subset-of-columns)
- [架构推理](#schema-inference)
- [filename 函数](#filename-function)
- [filepath 函数](#filepath-function)
- [处理复杂类型以及嵌套或重复的数据结构](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>查询 PARQUET 文件

若要查询 Parquet 源数据，请使用 FORMAT = 'PARQUET'

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

有关用法示例，请查看[查询 Parquet 文件](query-parquet-files.md)一文。

## <a name="query-csv-files"></a>查询 CSV 文件

若要查询 Parquet 源数据，请使用 FORMAT = 'CSV'。 查询 CSV 文件时，你可以将 CSV 文件的架构指定为 `OPENROWSET` 函数的一部分：

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

有一些其他选项可用于将分析规则调整为自定义 CSV 格式：
- ESCAPE_CHAR = 'char' 指定文件中用于将自身及文件中所有分隔符值转义的字符。 如果转义字符后接除本身以外的某个值或者任何分隔符值，则读取值时会删除该转义字符。
无论是否启用了 FIELDQUOTE，都会应用 ESCAPE_CHAR 参数。 不会使用该参数来转义引号字符。 必须使用其他引号字符来转义引号字符。 要让引号字符出现在列值内，必须将值放在引号中。
- FIELDTERMINATOR ='field_terminator' 指定要使用的字段终止符。 默认的字段终止符为逗号（“,”）
- ROWTERMINATOR ='row_terminator' 指定要使用的行终止符。 默认的行终止符为换行符：\r\n。 ****

## <a name="file-schema"></a>文件架构

Synapse SQL 中的 SQL 语言允许你将文件的模式定义为 `OPENROWSET` 函数的一部分，并读取所有列或列的子集，或者尝试使用架构推理自动确定文件中的列类型。

### <a name="read-a-chosen-subset-of-columns"></a>读取选定的列子集

若要指定所要读取的列，可以在 `OPENROWSET` 语句中提供可选的 WITH 子句。

- 如果存在 CSV 数据文件，若要读取所有列，请提供列名及其数据类型。 如果需要列的子集，请使用序号按顺序从来源数据文件中选取列。 列将按序号指定值绑定。
- 如果存在 Parquet 数据文件，请提供与来源数据文件中的列名匹配的列名。 列将按名称绑定。

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 as varchar(max)
) as rows
```

对于每个列，你需要在 `WITH` 子句中指定列名称和类型。
有关示例，请参阅[在不指定所有列的情况下读取 CSV 文件](query-single-csv-file.md#returning-subset-of-columns)。

## <a name="schema-inference"></a>架构推理

通过从 `OPENROWSET` 语句中省略 WITH 子句，可以指示服务从基础文件中自动检测（推理）架构。

> [!NOTE]
> 目前，这仅适用于 PARQUET 文件格式。

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

请确保使用[适当的推断数据类型](best-practices-sql-on-demand.md#check-inferred-data-types)以获得最佳性能。 

## <a name="query-multiple-files-or-folders"></a>查询多个文件或文件夹

若要针对一个文件夹或一组文件夹中的一组文件运行 T-SQL 查询，并将这些文件视为单个实体或行集，请提供某个文件夹的路径，或者提供针对一组文件或文件夹的模式（使用通配符）。

下列规则适用：

- 模式可以出现在目录路径的某个部分中，或者出现在文件名中。
- 多种模式可以出现在同一个目录步骤或文件名中。
- 如果有多个通配符，则所有匹配路径中的文件将包含在生成的文件集中。

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

有关用法示例，请参阅[查询文件夹和多个文件](query-folders-multiple-csv-files.md)。

## <a name="file-metadata-functions"></a>文件元数据函数

### <a name="filename-function"></a>Filename 函数

此函数返回行的来源文件的名称。 

若要查询特定的文件，请阅读[查询特定文件](query-specific-files.md#filename)一文中的“Filename”部分。

返回数据类型为 nvarchar(1024)。 为了获得最佳性能，请始终将 filename 函数的结果强制转换为适当的数据类型。 如果使用字符数据类型，请确保使用适当的长度。

### <a name="filepath-function"></a>Filepath 函数

此函数返回完整路径或一部分路径：

- 如果在不使用参数的情况下调用此函数，此函数将返回行的来源文件的完整路径。
- 如果在使用参数的情况下调用此函数，此函数将返回与该参数中指定的位置上的通配符相匹配的路径部分。 例如，参数值 1 将返回与第一个通配符匹配的路径部分。

有关更多信息，请阅读[查询特定文件](query-specific-files.md#filepath)一文的“Filepath”部分。

返回数据类型为 nvarchar(1024)。 为了获得最佳性能，请始终将 filepath 函数的结果强制转换为适当的数据类型。 如果使用字符数据类型，请确保使用适当的长度。

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>处理复杂类型以及嵌套或重复的数据结构

为了在处理以嵌套的或重复的数据类型存储的数据（例如，在 [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) 文件中）时实现顺畅的体验，SQL 按需版本添加了以下扩展。

#### <a name="project-nested-or-repeated-data"></a>投影嵌套数据或重复数据

若要投影数据，请对包含嵌套数据类型列的 Parquet 文件运行 SELECT 语句。 在输出中，嵌套值将序列化为 JSON，并作为 varchar (8000) SQL 数据类型返回。

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

有关更多详细信息，请参阅[查询 Parquet 嵌套类型](query-parquet-nested-types.md#project-nested-or-repeated-data)一文中的“投影嵌套数据或重复数据”部分。

#### <a name="access-elements-from-nested-columns"></a>访问嵌套列中的元素

若要访问嵌套列中的嵌套元素（例如 Struct），请使用“点表示法”将字段名称串联成路径。 在 `OPENROWSET` 函数的 WITH 子句中提供路径作为 column_name。

语法片段示例如下所示：

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

默认情况下，`OPENROWSET` 函数会将源字段名称和路径与 WITH 子句中提供的列名进行匹配。 可以通过 WITH 子句访问同一源 Parquet 文件中包含在不同嵌套级别的元素。

**返回值**

- 对于不在“嵌套类型”组中的所有 Parquet 类型，函数将返回指定元素以及指定路径中的某个标量值，例如 int、decimal 和 varchar。
- 如果该路径指向嵌套类型的元素，则函数将返回指定路径中从顶部元素开始的 JSON 片段。 JSON 片段的类型为 varchar (8000)。
- 如果在指定的 column_name 中找不到该属性，则函数将返回错误。
- 如果在指定的 column_path 中找不到该属性，则函数将根据[路径模式](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)返回结果：在严格模式下返回错误，在宽松模式下返回 null。

有关查询示例，请查看[查询 Parquet 嵌套类型](query-parquet-nested-types.md#access-elements-from-nested-columns)一文中的“访问嵌套列中的元素”部分。

#### <a name="access-elements-from-repeated-columns"></a>访问重复列中的元素

若要访问重复列中的元素（例如数组或映射的元素），请对需要投影的每个标量元素使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 函数，并提供：

- 嵌套列或重复列（作为第一个参数）
- 用于指定要访问的元素或属性的 [JSON 路径](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)（作为第二个参数）

若要访问重复列中的非标量元素，请对需要投影的每个非标量元素使用 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 函数，并提供：

- 嵌套列或重复列（作为第一个参数）
- 用于指定要访问的元素或属性的 [JSON 路径](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)（作为第二个参数）

请参阅以下语法片段：

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

可以在[查询 Parquet 嵌套类型](query-parquet-nested-types.md#access-elements-from-repeated-columns)一文中查找有关访问重复列中的元素的查询示例。

## <a name="query-samples"></a>查询示例

你可以通过示例查询了解有关查询各种类型数据的更多信息。

### <a name="tools"></a>工具

发出查询所需的工具：
    - Azure Synapse Studio（预览版）
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>演示设置

第一步是创建将在其中执行查询的数据库。 然后，可以通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 

此安装脚本将创建数据源、数据库范围的凭据和用于读取这些示例中数据的外部文件格式。

> [!NOTE]
> 数据库仅用于查看元数据，而不用于实际数据。  请记下使用的数据库名称，因为稍后需要用到。

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>提供的演示数据

演示数据包含以下数据集：

- 纽约市出租车 - 黄色出租车行程记录 - 采用 CSV 和 Parquet 格式的纽约市公共数据集的一部分
- 采用 CSV 格式的人口数据集
- 包含嵌套列的 Parquet 文件示例
- JSON 格式的书籍

| 文件夹路径                                                  | 说明                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | 采用 CSV 格式的数据的父文件夹                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | 采用不同 CSV 格式的人口数据文件的文件夹。 |
| /csv/taxi/                                                   | 采用 CSV 格式的纽约市公共数据文件的文件夹              |
| /parquet/                                                    | 采用 Parquet 格式的数据的父文件夹                     |
| /parquet/taxi                                                | 采用 Parquet 格式的纽约市公共数据文件，已使用 Hive/Hadoop 分区方案按年份和月份分区。 |
| /parquet/nested/                                             | 包含嵌套列的 Parquet 文件示例                     |
| /json/                                                       | 采用 JSON 格式的数据的父文件夹                        |
| /json/books/                                                 | 包含书籍数据的 JSON 文件                                   |


## <a name="next-steps"></a>后续步骤

有关如何查询不同文件类型以及创建和使用视图的详细信息，请参阅以下文章：

- [查询 CSV 文件](query-single-csv-file.md)
- [查询 Parquet 文件](query-parquet-files.md)
- [查询 JSON 文件](query-json-files.md)
- [查询嵌套值](query-parquet-nested-types.md)
- [查询文件夹和多个 CSV 文件](query-folders-multiple-csv-files.md)
- [在查询中使用文件元数据](query-specific-files.md)
- [创建和使用视图](create-use-views.md)
