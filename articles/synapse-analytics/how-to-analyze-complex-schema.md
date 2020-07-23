---
title: 用数组和嵌套结构分析架构
description: 如何在 Apache Spark 和 SQL 中分析数组和嵌套结构
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907900"
---
# <a name="analyze-complex-data-types-in-synapse"></a>分析 Synapse 中的复杂数据类型

本文适用于**Azure Cosmos DB 的 Azure Synapse 链接**中的 Parquet 文件和容器。 它说明用户如何使用 Spark 或 SQL 来读取或转换包含复杂架构（例如数组或嵌套结构）的数据。 以下示例是使用单个文档完成的，但可以使用 Spark 或 SQL 轻松扩展到数十亿个文档。 以下代码使用 PySpark （Python）。

## <a name="use-case"></a>用例

对于新式数据类型，复杂的数据类型通常是处理和表示数据工程师面临的难题。 分析嵌套的架构和阵列面临一些挑战：
* 编写 SQL 查询的复杂程度
* 难以重命名/强制转换嵌套列的数据类型
* 命中深层嵌套对象的性能问题

数据工程师需要了解如何有效地处理这些数据类型，并使所有人都可以轻松地对其进行访问。

在下面的示例中，Synapse Spark 用于通过数据帧将对象读取和转换为平面结构。 Synapse SQL 无服务器用于直接查询此类对象，并以常规表的形式返回这些结果。

## <a name="what-are-arrays-and-nested-structures"></a>什么是数组和嵌套结构？

以下对象来自应用见解。 在此对象中，存在嵌套结构，但也存在包含嵌套结构的数组。

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>数组和嵌套结构的架构示例
当用命令**printschema**打印该对象的数据帧的架构（称为**df**）时，将看到以下表示形式：

* 黄色表示嵌套结构
* 绿色表示具有两个元素的数组

[![架构源](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid、_ts 和 _etag 已添加到系统中，因为文档已引入到 Azure Cosmos DB 事务性存储区中。

上面的数据帧只计算5列和1行。 转换后，特选数据帧将有13列和2行（表格格式）。

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>利用 Apache Spark 平展嵌套结构和分解数组

借助 Synapse Spark，可以轻松地将嵌套结构转换为列，将数组元素转换为多个行。 每个用户可以使用以下步骤来实现其自己的实现。

[![Spark 转换步骤](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**步骤 1**：定义一个平展嵌套架构的函数。 此函数无需更改即可使用。 在 Pyspark 笔记本中使用该函数创建一个单元：

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**步骤 2**：使用函数将数据帧**df**的嵌套架构平展到新的数据帧中**df_flat**：

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

显示函数应返回10列和1行。 数组及其嵌套元素仍然存在。

**步骤 3**：现在，我们将数据帧中**context_custom_dimensions**的数组转换**df_flat**为新的数据帧**df_flat_explode**。 在下面的代码中，我们还定义了所选的列：

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

显示函数应返回以下结果：10列和2行。 下一步是将嵌套架构与步骤1中定义的函数平展在一起。

**步骤 4**：使用函数将数据帧**df_flat_explode**的嵌套架构平展到新的数据帧**df_flat_explode_flat**：
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

显示函数应显示13列和2行：

数据 df_flat_explode_flat 帧的函数 printSchema 将返回以下结果：

[![架构最终](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>直接用 SQL 无服务器读取数组和嵌套结构

通过 SQL 无服务器查询，可以通过此类对象来创建视图和表。

首先，根据数据的存储方式，用户应使用以下分类。 所有大写都是特定于用例的：

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" （ADLSg2）|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = ACCOUNTNAME; database = DATABASENAME; collection = COLLECTIONNAME; region = REGIONTOQUERY，SECRET = ' YOURSECRET ' |"CosmosDB" （Synapse 链接）|



**SQL 无服务器**将支持 AZURE COSMOS DB 和 AAD 传递的 Azure Synapse 链接的链接服务。 此功能当前位于 Synapse 链接的封闭预览版下。

替换如下：
* 你连接到的数据源的连接字符串的 "上大容量"
* 使用用于连接到源的格式的 "以上类型"

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

完成了两种不同类型的操作：
* 下面的代码行将定义名为 contextdataeventTime 的列，该列引用了嵌套元素： eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

此行将定义名为 contextdataeventTime 的列，该列引用了嵌套元素：上下文>数据>eventTime

* "**交叉应用**" 用于为数组下的每个元素创建新行，然后定义每个与第一个项目符号类似的嵌套对象： 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

如果数组有5个具有4个嵌套结构的元素，则 SQL 无服务器将返回5行和4列。

SQL 无服务器可以就地查询，将数组映射到2行，并将所有嵌套结构显示在列中。

## <a name="next-steps"></a>后续步骤

* [了解如何通过 Spark 查询 Azure Cosmos DB 的 Azure Synapse 链接](./synapse-link/how-to-query-analytical-store-spark.md)
* [查询 parquet 嵌套类型](./sql/query-parquet-nested-types.md) 