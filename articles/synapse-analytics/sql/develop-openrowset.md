---
title: 如何使用 SQL 按需版本（预览版）中的 OPENROWSET
description: 本文介绍 SQL 按需版本（预览版）中 OPENROWSET 的语法，并说明如何使用参数。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4ec6e18aa4fa741ba784e68ccf9b5f87ad654eba
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591414"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>如何使用 SQL 按需版本（预览版）中的 OPENROWSET

`OPENROWSET(BULK...)` 函数可用于访问 Azure 存储中的文件。 `OPENROWSET` 函数读取远程数据源（例如文件）的内容，并将内容作为行集返回。 在 SQL 按需版本（预览版）资源中，可以通过调用 OPENROWSET 函数并指定 BULK 选项，来访问 OPENROWSET BULK 行集提供程序。  

`OPENROWSET` 函数可以在查询的 `FROM` 子句中引用，就好象它是表名 `OPENROWSET` 一样。 该函数通过内置的 BULK 提供程序（用于从文件中读取数据并将数据作为行集返回）支持批量操作。

## <a name="data-source"></a>数据源

Synapse SQL 中的 OPENROWSET 函数从数据源读取文件的内容。 数据源是一个 Azure 存储帐户，它可以在 `OPENROWSET` 函数中显式引用，也可以基于你要读取的文件的 URL 动态推断。
可以选择让 `OPENROWSET` 函数包含 `DATA_SOURCE` 参数，以指定包含文件的数据源。
- 不带 `DATA_SOURCE` 的 `OPENROWSET` 可用来从指定为 `BULK` 选项的 URL 位置直接读取文件的内容：

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://storage..../container/folder/*.parquet',
                    TYPE = 'PARQUET') AS file
    ```

这是一种无需预先配置即可读取文件内容的简单快捷方法。 有了此选项，就可以使用基本身份验证选项来访问存储（使用 Azure AD 直通身份验证进行 Azure AD 登录，使用 SAS 令牌进行 SQL 登录）。 

- 带 `DATA_SOURCE` 的 `OPENROWSET` 可用来访问指定存储帐户中的文件：

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    TYPE = 'PARQUET') AS file
    ```

    使用此选项，可以配置数据源中的存储帐户的位置，并指定应该用来访问存储的身份验证方法。 
    
    > [!IMPORTANT]
    > 不带 `DATA_SOURCE` 的 `OPENROWSET` 提供了快速轻松地访问存储文件的方法，但提供的身份验证选项有限。 例如，Azure AD 主体只能使用其 [Azure AD 标识](develop-storage-files-storage-access-control.md?tabs=user-identity#force-azure-ad-pass-through)来访问文件，不能访问公开提供的文件。 如果需要更强大的身份验证选项，请使用 `DATA_SOURCE` 选项，并定义需要将其用来访问存储的凭据。

## <a name="security"></a>安全性

数据库用户必须拥有 `ADMINISTER BULK OPERATIONS` 权限才能使用 `OPENROWSET` 函数。

存储管理员还必须提供有效的 SAS 令牌或允许 Azure AD 主体访问存储文件，从而使用户能够访问文件。 请通过[此文](develop-storage-files-storage-access-control.md)详细了解存储访问控制。

`OPENROWSET` 使用以下规则来确定如何向存储进行身份验证：
- 在带 `DATA_SOURCE` 的 `OPENROWSET` 中，身份验证机制依赖于调用方类型。
  - 如果 Azure 存储允许 Azure AD 用户访问基础文件（例如，如果调用方对存储具有“存储读取者”权限），并且你在 Synapse SQL 服务上[启用 Azure AD 直通身份验证](develop-storage-files-storage-access-control.md#force-azure-ad-pass-through)，则 AAD 登录名只能使用其自己的 [Azure AD 标识](develop-storage-files-storage-access-control.md?tabs=user-identity#force-azure-ad-pass-through)来访问文件。
  - SQL 登录名还可以使用不带 `DATA_SOURCE` 的 `OPENROWSET` 来访问公开可用的文件、使用 SAS 令牌或 Synapse 工作区的托管标识保护的文件。 你需要[创建服务器范围的凭据](develop-storage-files-storage-access-control.md#examples)，以便访问存储文件。 
- 在带 `DATA_SOURCE` 的 `OPENROWSET` 中，身份验证机制是在分配给被引用数据源的数据库范围的凭据中定义的。 使用此选项，可以访问公开可用的存储，或者使用 SAS 令牌、工作区的托管标识或[调用方的 Azure AD 标识](develop-storage-files-storage-access-control.md?tabs=user-identity#)（如果调用方是 Azure AD 主体）来访问存储。 如果 `DATA_SOURCE` 引用了非公共的 Azure 存储，则你需要[创建数据库范围的凭据](develop-storage-files-storage-access-control.md#examples)并在 `DATA SOURCE` 中引用该凭据以便访问存储文件。

调用方必须对凭据具有 `REFERENCES` 权限，才能使用它向存储进行身份验证。

## <a name="syntax"></a>语法

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>参数

对于包含要查询的目标数据的输入文件，可以采用两个选项。 有效值是：

- 'CSV' - 包含带有行/列分隔符的任何分隔式文本文件。 任何字符（例如 TSV）均可用作字段分隔符：FIELDTERMINATOR = tab。

- 'PARQUET' - Parquet 格式的二进制文件 

'unstructured_data_path'

用于建立数据路径的 unstructured_data_path 可以是绝对路径，也可以是相对路径：
- 绝对路径的格式为“\<prefix>://\<storage_account_path>/\<storage_path>”，允许用户直接读取文件。
- 相对路径的格式为“<storage_path>”，必须与 `DATA_SOURCE` 参数一起使用，它描述 `EXTERNAL DATA SOURCE` 定义的 <storage_account_path> 位置中的文件模式。 

 在下面可以找到相关的 <storage account path> 值，用于链接到特定的外部数据源。 

| 外部数据源       | 前缀 | 存储帐户路径                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob 存储         | https  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>.dfs.core.windows.net              |
||||

'\<storage_path>'

 指定存储中的的路径，该路径指向所要读取的文件夹或文件。 如果路径指向某个容器或文件夹，则会读取该特定容器或文件夹中的所有文件。 不包括子文件夹中的文件。 

 可以使用通配符将目标指定为多个文件或文件夹。 允许使用多个不连续的通配符。
下面的示例从以“/csv/population”开头的所有文件夹中读取以“population”开头的所有 csv 文件：    
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

如果将 unstructured_data_path 指定为某个文件夹，则 SQL 按需版本查询将从该文件夹中检索文件。 

> [!NOTE]
> 与 Hadoop 和 PolyBase 不同，SQL 按需版本不返回子文件夹。 此外，与 Hadoop 和 PloyBase 不同，SQL 按需版本会返回文件名以下划线 (_) 或句点 (.) 开头的文件。

在以下示例中，如果 unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`，则 SQL 按需版本查询将返回 mydata.txt 和 _hidden.txt 中的行。 它不返回 mydata2.txt 和 mydata3.txt，因为这些文件位于子文件夹中。

![外部表的递归数据](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

使用 WITH 子句可以指定要从文件中读取的列。

- 对于 CSV 数据文件，若要读取所有列，请提供列名及其数据类型。 如果需要列的子集，请使用序号按顺序从来源数据文件中选取列。 列将按序号指定值绑定。 

    > [!IMPORTANT]
    > 对于 CSV 文件，WITH 子句是必需的。
    >
    
- 对于 Parquet 数据文件，请提供与来源数据文件中的列名匹配的列名。 列将按名称绑定。 如果省略 WITH 子句，将返回 Parquet 文件中的所有列。

column_name = 输出列的名称。 如果提供此名称，此名称将替代源文件中的列名。

column_type = 输出列的数据类型。 将在此处发生隐式数据类型转换。

column_ordinal = 列在源文件中的序号。 对于 Parquet 文件，将忽略此参数，因为绑定是按名称进行的。 以下示例仅返回 CSV 文件中的第二列：

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

\<bulk_options>

FIELDTERMINATOR ='field_terminator'

指定要使用的字段终止符。 默认的字段终止符为逗号（“,”）。

ROWTERMINATOR ='row_terminator'`

指定要使用的行终止符。 如果未指定行终止符，将使用默认终止符之一。 PARSER_VERSION = '1.0' 的默认终止符为 \r\n、\n 和 \r。 PARSER_VERSION = '2.0' 的默认终止符为 \r\n 和 \n。

ESCAPE_CHAR = 'char'

指定文件中用于将自身及文件中所有分隔符值转义的字符。 如果转义字符后接除本身以外的某个值或者任何分隔符值，则读取值时会删除该转义字符。 

无论是否启用了 FIELDQUOTE，都会应用 ESCAPE_CHAR 参数。 不会使用该参数来转义引号字符。 将会根据 Excel CSV 的行为使用双引号来转义引号字符。

FIRSTROW = 'first_row' 

指定要加载的第一行的行号。 默认值为 1。 这表示指定数据文件中的第一行。 通过对行终止符进行计数来确定行号。 FIRSTROW 从 1 开始。

FIELDQUOTE = 'field_quote' 

指定将用作 CSV 文件引号字符的字符。 如果未指定，将使用引号字符 (")。 

DATA_COMPRESSION = 'data_compression_method'

指定压缩方法。 支持以下压缩方法：

- org.apache.hadoop.io.compress.GzipCodec

PARSER_VERSION = 'parser_version'

指定读取文件时要使用的分析器版本。 当前支持的 CSV 分析器版本为 1.0 和 2.0

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

CSV 分析器版本 1.0 是默认版本且功能丰富，而 2.0 是为提高性能而构建的，并不支持所有选项和编码。 

CSV 分析器版本 2.0 详细信息：

- 不是所有数据类型都受支持。
- 最大行大小限制为 8MB。
- 不支持使用以下选项：DATA_COMPRESSION。
- 带引号的空字符串 ("") 被解释为空字符串。

## <a name="examples"></a>示例

以下示例仅返回 population*.csv 文件中序号为 1 和 4 的两列。 由于文件中没有标题行，因此该示例从第一行开始读取：

```sql
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

以下示例在不指定列名和数据类型的情况下，以 Parquet 格式返回人口普查数据集内第一行的所有列： 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

如果收到错误消息，指出无法列出文件，则需要在 Synapse SQL 按需版本中启用对公共存储的访问权限：
- 如果使用 SQL 登录，则需要[创建服务器范围的凭据以允许访问公共存储](develop-storage-files-storage-access-control.md#examples)。
- 如果你使用 Azure AD 主体来访问公共存储，则需要[创建服务器范围的凭据以允许访问公共存储](develop-storage-files-storage-access-control.md#examples)并禁用 [Azure AD 直通身份验证](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)。

## <a name="next-steps"></a>后续步骤

有关更多示例，请参阅[查询数据存储快速入门](query-data-storage.md)，了解如何使用 OPENROWSET 来读取 [CSV](query-single-csv-file.md)、[PARQUET](query-parquet-files.md) 和 [JSON](query-json-files.md) 文件格式。 你还可以了解如何使用 [CETAS](develop-tables-cetas.md) 将查询结果保存到 Azure 存储。
