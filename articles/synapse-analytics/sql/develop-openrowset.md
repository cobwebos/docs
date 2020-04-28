---
title: 如何使用 SQL 按需版本（预览版）中的 OPENROWSET
description: 本文介绍 SQL 按需版本（预览版）中 OPENROWSET 的语法，并说明如何使用参数。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680490"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>如何使用 SQL 按需版本（预览版）中的 OPENROWSET

OPENROWSET(BULK...) 函数可用于访问 Azure 存储中的文件。 在 SQL 按需版本（预览版）资源中，可以通过调用 OPENROWSET 函数并指定 BULK 选项，来访问 OPENROWSET BULK 行集提供程序。  

可以在查询的 FROM 子句中像引用表名 OPENROWSET 那样引用 OPENROWSET 函数。 该函数通过内置的 BULK 提供程序（用于从文件中读取数据并将数据作为行集返回）支持批量操作。

SQL 池目前不支持 OPENROWSET。

## <a name="syntax"></a>语法

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>参数

对于包含要查询的目标数据的输入文件，可以采用两个选项。 有效值是：

- 'CSV' - 包含带有行/列分隔符的任何分隔式文本文件。 任何字符（例如 TSV）均可用作字段分隔符：FIELDTERMINATOR = tab。

- 'PARQUET' - Parquet 格式的二进制文件 

'unstructured_data_path' 

用于建立数据路径的 unstructured_data_path 的结构如下所示：  
'\<prefix>://\<storage_account_path>/\<storage_path>'
 
 
 在下面可以找到用于链接到特定外部数据源的相关存储帐户路径。 

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

指定要使用的行终止符。 默认的行终止符为 \r\n 之类的换行符。

ESCAPE_CHAR = 'char'

指定文件中用于将自身及文件中所有分隔符值转义的字符。 如果转义字符后接除本身以外的某个值或者任何分隔符值，则读取值时会删除该转义字符。 

无论是否启用了 FIELDQUOTE，都会应用 ESCAPE_CHAR 参数。 不会使用该参数来转义引号字符。 将会根据 Excel CSV 的行为使用双引号来转义引号字符。

FIRSTROW = 'first_row' 

指定要加载的第一行的行号。 默认值为 1。 这表示指定数据文件中的第一行。 通过对行终止符进行计数来确定行号。 FIRSTROW 从 1 开始。

FIELDQUOTE = 'field_quote' 

指定将用作 CSV 文件引号字符的字符。 如果未指定，将使用引号字符 (")。 

## <a name="examples"></a>示例

以下示例仅返回 population*.csv 文件中序号为 1 和 4 的两列。 由于文件中没有标题行，因此该示例从第一行开始读取：

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

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
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>后续步骤

有关更多示例，请访问[快速入门](query-data-storage.md)，或使用 [CETAS](develop-tables-cetas.md) 将查询结果保存到 Azure 存储。
