---
title: 用数组和嵌套结构分析架构
description: 如何在 Apache Spark 和 SQL 中分析数组和嵌套结构
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 43e4027c9d88b209ed0606e045dd5b4d47d3ff6e
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088510"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>分析 Azure Synapse 分析中的复杂数据类型

本文适用于 [Azure Cosmos DB 的 Azure Synapse 链接](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md)中的 Parquet 文件和容器。 您可以使用 Spark 或 SQL 来读取或转换包含复杂架构（如数组或嵌套结构）的数据。 以下示例是使用单个文档完成的，但可以使用 Spark 或 SQL 轻松扩展到数十亿个文档。 本文中包含的代码使用 PySpark (Python) 。

## <a name="use-case"></a>使用案例

复杂的数据类型越来越常见，为数据工程师带来了挑战。 分析嵌套的架构和数组可能涉及耗时且复杂的 SQL 查询。 此外，可能很难重命名或强制转换嵌套列数据类型。 此外，在使用深度嵌套的对象时，可能会遇到性能问题。

数据工程师需要了解如何有效地处理复杂的数据类型，使每个人都可以轻松地对其进行访问。 在下面的示例中，将在 Azure Synapse Analytics 中使用 Spark，通过数据帧将对象读取和转换为平面结构。 使用 Azure Synapse Analytics 中的 SQL 无服务器模型直接查询此类对象，并以常规表的形式返回这些结果。

## <a name="what-are-arrays-and-nested-structures"></a>什么是数组和嵌套结构？

以下对象来自 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 在此对象中，有嵌套结构和包含嵌套结构的数组。

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
如果要打印对象的数据帧的架构 (通过命令调用的是 **df**) `df.printschema` ，可以看到以下表示形式：

* 黄色表示嵌套结构。
* 绿色表示具有两个元素的数组。

[![突出显示黄色和绿色突出显示的代码，显示架构源](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`已将、 `_ts` 和 `_etag` 添加到系统中，因为将文档引入到 Azure Cosmos DB 的事务性存储区中。

前面的数据帧只计算5列和1行。 转换后，特选数据帧将有13列和2行（表格格式）。

## <a name="flatten-nested-structures-and-explode-arrays"></a>平展嵌套结构和分解数组

借助 Azure Synapse 分析中的 Spark，可以轻松地将嵌套结构转换为列，将数组元素转换为多个行。 使用以下步骤实现。

[![显示 Spark 转换步骤的流程图](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>定义用于平展嵌套架构的函数

无需更改即可使用此函数。 使用以下函数在 [PySpark 笔记本](quickstart-apache-spark-notebook.md) 中创建单元：

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

### <a name="use-the-function-to-flatten-the-nested-schema"></a>使用函数平展嵌套架构

在此步骤中，您将数据帧的嵌套架构 (**df**) 平展到新的数据帧 (`df_flat`) ：

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

显示函数应返回10列和1行。 数组及其嵌套元素仍然存在。

### <a name="transform-the-array"></a>转换数组

此处，将 `context_custom_dimensions` 数据帧中的数组转换 `df_flat` 为新的数据帧 `df_flat_explode` 。 在下面的代码中，还将定义要选择的列：

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

显示函数应返回10列和2行。 下一步是将嵌套架构与步骤1中定义的函数平展在一起。

### <a name="use-the-function-to-flatten-the-nested-schema"></a>使用函数平展嵌套架构

最后，使用函数将数据帧的嵌套架构平展 `df_flat_explode` 到一个新的数据帧中 `df_flat_explode_flat` ：
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

显示函数应显示13列和2行。

`printSchema`数据帧的功能 `df_flat_explode_flat` 返回以下结果：

[![显示最终架构的代码](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>直接读取数组和嵌套结构

通过 SQL 的无服务器模型，你可以查询和创建此类对象的视图和表。

首先，根据数据的存储方式，用户应使用以下分类。 大写字母显示的所有内容都是特定于你的用例：

| 批量 | 格式 |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2) |
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = ACCOUNTNAME; database = DATABASENAME; collection = COLLECTIONNAME; region = REGIONTOQUERY '，SECRET = ' YOURSECRET ' | (Azure Synapse 链接上的 "CosmosDB") |


按如下所示替换每个字段：
* "你的上大容量" 是你连接到的数据源的连接字符串。
* "以上类型" 是用于连接到源的格式。

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

有两种不同类型的操作：

- 第一种操作类型在下面的代码行中指示，后者定义 `contextdataeventTime` 引用嵌套元素的名为的列 `Context.Data.eventTime` 。 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  此行定义 `contextdataeventTime` 引用嵌套元素的名为的列 `Context>Data>eventTime` 。

- 第二种操作类型用于 `cross apply` 为数组下的每个元素创建新行。 然后，它定义每个嵌套的对象。 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  如果数组有5个具有4个嵌套结构的元素，则 SQL 的无服务器模型将返回5行和4列。 SQL 的无服务器模型可以就地查询，将数组映射到2行，并将所有嵌套结构显示在列中。

## <a name="next-steps"></a>后续步骤

* [了解如何通过 Spark 查询 Azure Cosmos DB 的 Synapse 链接](./synapse-link/how-to-query-analytical-store-spark.md)
* [查询 Parquet 嵌套类型](./sql/query-parquet-nested-types.md) 
